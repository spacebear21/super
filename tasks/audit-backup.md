# Back up the payjoin-audit repo on GitHub

## What it is
The audit repo at ~/repos/payjoin-audit (findings registry, reports, interop matrix, nightly pipeline) lives only on the box's persistent volume. The super repo is pushed to GitHub every few minutes; the audit repo has no remote at all.

## Where it stands
Checked 2026-11-07 after the deploy: no remote configured, and `spacebear21/payjoin-audit` does not exist on GitHub. This was the one leftover from the deploy task; the deploy itself is done.

## What is asked
Create the repository on GitHub (private, since the registry holds unpublished findings), then tell me its name. I add the remote and the same sync the super repo uses, with the write token the image already carries for the super repo if it covers the new repo.
