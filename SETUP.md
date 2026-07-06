# Setup on a new PC

Source of truth: this repo — https://github.com/davidyounger1-design/myappbuddy-meta

```
git clone https://github.com/davidyounger1-design/myappbuddy-meta.git ~/myappbuddy-meta
ln -s ~/myappbuddy-meta/CLAUDE.md ~/.claude/CLAUDE.md
```

(Windows without symlink permission — e.g. Developer Mode not enabled: `copy`
the file instead, and re-copy it after every edit. A symlink is preferred
because it can't silently drift out of date.)

Then, once per machine (these do **not** sync — each PC authenticates itself):

```
gh auth login
supabase login
```

Also register that machine's SSH public key in the relevant Hostinger hPanel
(see "Hostinger SSH access" in `CLAUDE.md`) — each PC needs its own keypair.

**To edit `CLAUDE.md`:** edit it in the `myappbuddy-meta` repo, commit, push,
then on the other PC: `git -C ~/myappbuddy-meta pull` (re-copy on Windows if
not symlinked).
