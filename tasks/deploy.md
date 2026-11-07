# Deploy hub main to the workhorse

## What it is
The harness image is built from the hub's main and deployed from your laptop. Until then, new scripts live only in worktrees and anything on the rootfs is lost at the next rebuild.

## What is on hub main since the last deploy
- The phone dashboard: the Tasks board, the Tasks and Agents pages, and a task detail page with in place replies (being built now).
- The memory jobs: `transcripts-index`, `super-dream`, and schedule entries for the dream (05:30 UTC) and the interop matrix (04:45 UTC).
- Earlier: Token Factory model changes in the OpenCode config, the scheduler, super-watch fixes.

## What is asked
1. Pull hub main on the laptop and run `infra/scripts/workhorse-deploy.sh`, outside the 02:17 UTC audit window.
2. Confirm the super-sync PAT (`SUPER_GH_TOKEN`) is set in the image; the super repo must keep pushing after the rebuild.
3. `spacebear21/payjoin-audit` does not exist on GitHub: create it or point the audit repo's remote elsewhere, so the audit repo is backed up like the super repo.
4. After the rebuild, `/login` in the super pane.
