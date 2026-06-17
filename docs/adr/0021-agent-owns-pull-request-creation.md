# The agent opens the pull request, not Sandcastle

## Context

We want a `pull-request` **branch strategy**: when the **agent** finishes its
**task**, a pull request is opened, linked to the **task** it worked on. The
obvious mental model — and the one the name "merge strategy" suggested — is that
Sandcastle runs `gh pr create` during run finalization, the same way
**merge-to-head** runs `git merge` itself.

That model breaks on a fact about how Sandcastle works: **Sandcastle never knows
which task the agent worked on.** The agent selects its own task inside the
sandbox by running `LIST_TASKS_COMMAND` / `VIEW_TASK_COMMAND` (`gh issue list …`,
driven by the prompt template). Sandcastle's control flow only ever sees the
**completion signal** (`<promise>COMPLETE</promise>`), which carries no payload
by design, and a single `run()` can span multiple **iterations** touching
multiple tasks. So "the issue it worked on" is not a value Sandcastle holds.

There were three ways to get the issue linkage:

1. The agent reports the issue number back via **structured output**, and
   Sandcastle runs `gh pr create` host-side using it.
2. Sandcastle opens an unlinked PR (drops the "linked to the issue" goal).
3. The agent opens the PR itself, since it is the only component that knows the
   task.

A second constraint shaped the consequences: `gh pr create` requires the branch
to already exist on the remote, and the agent creates the PR as the _last step
of its task, inside the sandbox_. Sandcastle's finalization runs **after** the
agent is done — too late to push "for" the agent. So whoever opens the PR must
also push, from inside the sandbox.

## Decision

The **agent** owns both the push and the `gh pr create`, as the final step of
its task, from inside the sandbox. Sandcastle does not run `gh pr create` and
does not verify the PR was created.

The `pull-request` branch strategy's job is therefore _not_ to perform the PR,
but to guarantee the preconditions the agent needs:

- **Credential/remote setup**, built into `SandboxLifecycle` (alongside the
  existing `user.name`/`user.email`/`safe.directory` config): `gh auth
setup-git`, normalize an SSH `origin` to an HTTPS push URL, and force-disable
  commit signing via `GIT_CONFIG_*` env.
- **Pre-flight fail-fast**: error before burning an agent run if `GH_TOKEN` is
  absent, `origin` is missing or not a GitHub URL, or `gh` is not installed.
- A **prompt-template convention** (scaffolded by `init`) instructing the agent
  to push and run `gh pr create --body "Closes #<ID>"`, mirroring the existing
  `CLOSE_TASK_COMMAND` convention. Sandcastle cannot inject the issue number —
  only the agent knows it — so the instruction is prose the agent fills in.

The issue linkage is thereby **correct by construction**: the one component that
knows the task is the one that writes `Closes #<ID>`.

## Considered Options

1. **Structured-output handoff** (rejected) — couples `pull-request` to
   `Output.object({ … })`, forces every PR run to configure structured output,
   and makes the strategy do orchestration we otherwise don't need. The only
   thing it buys is host-side `gh pr create`, which has no advantage over the
   agent doing it.
2. **Sandcastle opens an unlinked PR** (rejected) — drops the explicit "linked
   to the issue" requirement, which was the point.
3. **Agent owns push + PR** (chosen) — the issue link is correct by
   construction, reuses the `gh` + token the agent already has in the sandbox,
   and keeps the strategy thin (setup + fail-fast, no result plumbing).

## Consequences

- **No post-hoc verification.** Because the run resolves on the payload-free
  completion signal, Sandcastle cannot know whether the PR landed. A silently
  failed push reports run success; the failure shows only in the agent's output.
  Accepted (Q-fail-fast): pre-flight catches the 90% case; verifying the PR
  would drag structured output back in for marginal benefit.
- **HTTPS-only auth.** Push and `gh pr create` both authenticate over HTTPS with
  `GH_TOKEN` (scopes: Contents R/W + Pull requests R/W on top of the existing
  Issues R/W + Metadata R). We deliberately do _not_ forward the host SSH agent:
  an SSH signer gated behind a passphrase or biometric (e.g. 1Password) would
  prompt on every signed commit and on push, which is fatal to unattended runs.
  Commit signing is force-disabled for the same reason. SSH `origin`s are
  normalized to HTTPS so SSH-configured repos still work.
- **Isolated providers are excluded at the type level.** `pull-request` is in
  `BindMountBranchStrategy` and `NoSandboxBranchStrategy` only — not
  `IsolatedBranchStrategy` (mirroring how `head` is excluded). An isolated
  sandbox clones from a git **bundle**, so its in-sandbox `origin` is a local
  file, not the GitHub remote, and its commit SHAs are rewritten by
  `format-patch`/`am` on sync-out — it cannot push a PR branch to GitHub.
- **The strategy is "thin" but real.** It performs genuine `SandboxLifecycle`
  setup (credentials, remote normalization, signing), so it is more than a
  prompt template — but the act of opening the PR lives with the agent, not
  Sandcastle.
- **The PR base is unpinned.** Sandcastle does not pass `--base`; the agent's
  `gh pr create` defaults to the repo's default branch. A non-default base is a
  prompt-template edit.
