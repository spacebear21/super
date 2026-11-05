# super-watch-socket: done, pushed, installed; two things for you to apply

Branch `super-watch-socket` in `~/repos/harness-super-watch-socket`, off hub main at 7a7d1a2, pushed with `git push ~/repos/harness.git HEAD:main`, so hub main is e90cc32.

- 34be6dd Wake the super agent through its inbox socket (script, docstring, runbook Super agent section and troubleshooting)
- 51ab280 Let super-watch reach the super agent's inbox socket (seed `etc/super-settings.json` hook, managed settings `crossSessionInbound: accept`)
- e90cc32 Record the inbox socket wake-up route (progress.md)

## What changed

`super-watch` now sends each wake-up line through your session's inbox socket when it can: it reads `~/.local/state/super-watch/inbox-socket` (socket path on line 1, session token on line 2, mode 600, written by a SessionStart hook in your settings), connects, writes `{"type":"auth","token":…}` then `{"type":"user","message":{"role":"user","content":"[super-watch] …"}}`, newline-delimited, the format Claude Code's own debug hint gives. Claude Code starts a turn with it when you are idle, or reads it between tool calls when you are busy; it arrives as a user message wrapped in Claude Code's peer-message preamble ("Another Claude session sent a message: …") with the line intact, and nothing touches your input line. Without the file, or when the socket is gone, it types into your pane as before, behind the typing hold. Every wake-up logs its route: `N events sent by socket /tmp/cc-socks/<pid>.sock` or `N events sent by typing into wW:p1`. Deleted-note check unchanged, standard library only.

The new copy is installed at `/usr/local/bin/super-watch` and running as pid 930811 under the entrypoint loop (877016 killed). Until you apply the two things below it types, as it logs.

## 1. Add the hook to your live `~/super/.claude/settings.json`

Exact JSON to merge in (the seed `infra/workhorse/etc/super-settings.json` is the same file in full):

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "umask 077; d=\"$HOME/.local/state/super-watch\"; mkdir -p \"$d\" && if [ -n \"$CLAUDE_CODE_MESSAGING_SOCKET\" ] && [ -n \"$CLAUDE_CODE_MESSAGING_TOKEN\" ]; then printf '%s\\n%s\\n' \"$CLAUDE_CODE_MESSAGING_SOCKET\" \"$CLAUDE_CODE_MESSAGING_TOKEN\" > \"$d/inbox-socket.tmp\" && mv -f \"$d/inbox-socket.tmp\" \"$d/inbox-socket\"; else rm -f \"$d/inbox-socket\"; fi >/dev/null 2>&1; exit 0",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

It runs on startup, resume, `/clear` and compaction, prints nothing (SessionStart stdout would enter your context), and removes the file when the session has no socket so the watcher falls back. After adding it, `/clear` or restart the session so it fires; `ls -l ~/.local/state/super-watch/inbox-socket` should then show a 600 file whose first line matches the `Peer address` row of `/status`.

## 2. Make your session accept the message: not where the task expected

The task asked for `crossSessionInbound` in your project settings. That does not work, and I checked it rather than assume: a project or local value applies only when it is stricter than what managed, `--settings` or user settings give (settings reference), and on a scratch session with `"crossSessionInbound": "accept"` in its project settings the watcher's message still landed as an approval dialog, "from an unidentified session … did not attest its permission mode, and this session bypasses permission prompts". A script cannot attest: on Linux Claude Code verifies an own-child message by process ancestry, the watcher is not your child, and the token in the auth line is consulted only where ancestry is unavailable.

So the commit puts `"crossSessionInbound": "accept"` in the repo's managed settings (`infra/workhorse/etc/claude-managed-settings.json`, baked to `/etc/claude-code/managed-settings.json`). That is box-wide: every session here delivers unattested messages from the `agent` uid, which could already type into any pane through Herdr, so it widens nothing in practice; but it is a policy change, so I did not apply it live. The image deploy applies it. To apply it now, as agent:

```
sudo install -m 644 ~/repos/harness-super-watch-socket/infra/workhorse/etc/claude-managed-settings.json /etc/claude-code/managed-settings.json
```

Claude Code re-reads settings files on change; if a wake-up still lands as a dialog, restart the session. The narrow alternative, if you would rather not change the box policy: start your session with `cd ~/super && claude --settings '{"crossSessionInbound":"accept"}'`, which scopes it to you alone, but Herdr's restore after a deploy runs `claude --resume <id>` without the flag, so after each deploy you would be typed to until restarted by hand.

## Verification

Mocked driver: the socket route sends exactly the two lines and nothing else, with no screen read; a dead socket, a missing file and a malformed file fall back to typing with the log saying why; a socket that takes the line but never closes still counts as sent (no acknowledgement exists, and typing as well would wake you twice); the deleted-note check and the typing hold still apply. The earlier drivers still pass.

End to end, on a scratch Claude Code 2.1.318 session in a sibling pane under /tmp/r3-scratch, started with the per-session flag: its hook wrote the file; a hand-run watcher pointed at it (`SUPER_DIR`, `SUPER_WATCH_SOCKET_FILE`) woke the idle session within 5 s of a note landing, status idle to working, with the input box empty throughout and the transcript holding the exact line inside the preamble; after moving the file away the next note went by typing, which the scratch session itself reported as "delivered as typed input". Before the flag, with no setting and then with project-level accept, the same message was held as an approval dialog both times. The scratch session, its settings, its inbox and its state file are removed and its pane is closed. Drivers are session scratch, not in the tree.

Noticed: Claude Code treats a message whose sender pid is a child of some session as a reply address, so the scratch session answered each wake-up by messaging my session; from the entrypoint loop the watcher has no such parent and you get no reply address, which is right. Old `/tmp/cc-socks/<pid>.sock` files of ended sessions linger; the watcher's connect fails on them and it types instead.
