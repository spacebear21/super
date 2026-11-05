# super-sync-token: done, pushed to hub main, live loop untouched

Branch `super-sync-token` in `~/repos/harness-super-sync-token`, off hub main at 1a2c5fa, pushed with `git push ~/repos/harness.git HEAD:main`, so hub main is 7a7d1a2.

- 67269a9 Pass the write token through the environment (entrypoint, runbook secret inventory and Super agent wording)
- 7a7d1a2 Record the write token environment fix (progress.md)

What changed: both places where the entrypoint handed `SUPER_GH_TOKEN` to a process as an argument to `env`, the super-sync loop and the boot clone, now use `sudo --preserve-env=SUPER_GH_TOKEN -Hu agent ...`. The value goes from the entrypoint's environment (root-only) into the agent process's environment (that uid and root), and `ps` shows only the name. The stock sudoers allows it: an ALL rule implies SETENV, checked on this box. The credential helper string on git's command line names the variable but holds no value, as before.

The other secrets were already clean, checked line by line: `GH_TOKEN` and `TFF_API_KEY` reach login shells through `/etc/profile.d/90-secrets.sh` (root:agent, 640, rewritten each boot), never an argument; the dashboard loop unsets the token it does not need; `tailscaled` starts with a scrubbed environment; agents-refresh, dashboard-refresh and workhorse-herdr take no secret; `super-sync` reads the token from its environment only.

Verified as root with a dummy value read from a file so no test argv held it: the agent-uid child had the same value in its environment (compared by sha256), zero processes carried it in their arguments, the clone's credential helper returned it via `git credential fill`, and without the flag the child got nothing. `bash -n` and shellcheck at warning level pass, as they did before.

Live state, not touched: pid 455 (`sudo ... env SUPER_GH_TOKEN=...`, root, the leak), its loop shell 457 and the current `super-sync` pass 459, both as agent. The fix applies for good at the next image deploy, when the entrypoint starts the loop the new way. To apply it live before then, this, run as agent (it needs sudo for pid 1's environment and for killing 455); it reads the token from the entrypoint's environment, ends the old loop, and starts the new one detached, logging to `~/.local/state/super-sync.log` rather than `docker logs`:

```
sudo -n bash -c '
  export SUPER_GH_TOKEN=$(tr "\0" "\n" < /proc/1/environ | sed -n "s/^SUPER_GH_TOKEN=//p")
  [ -n "$SUPER_GH_TOKEN" ] || { echo "no token in pid 1 environment"; exit 1; }
  kill 455 457
  sudo --preserve-env=SUPER_GH_TOKEN -Hu agent setsid -f bash -c "while :; do /usr/local/bin/super-sync; sleep 10; done" >>/home/agent/.local/state/super-sync.log 2>&1
'
```

Then `ps -eo pid,user,args | grep -F "[s]uper-sync"` should show the new loop as agent with no value on any line, and 459, if it was mid-pass, finishes on its own. Best run between passes (the loop sleeps five minutes between them) so no push is cut short; a cut push is retried next pass anyway. The hand-started loop dies with the container, which is fine because the deployed entrypoint replaces it.

Nothing else needed from you.
