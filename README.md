# agent-scope

Providing a safe scope for CLI AI agents using permissions and ACLs.

`agent_scope` runs a selected CLI tool as an unprivileged agent user, grants temporary ACL access to one source tree, and bind-mounts that tree into `/srv/work/<tool>` by default.

## Dependencies

Required system tools:

- `bash`
- `sudo`
- `acl` tools: `getfacl`, `setfacl`
- `mount` tools: `mount`, `umount`, `mountpoint`, `findmnt`
- user/group tools: `id`, `getent`, `groupadd`, `groupdel`, `useradd`, `userdel`, `passwd`, `gpasswd`
- filesystem tools: `mkdir`, `mktemp`, `chmod`, `chown`, `ln`, `mv`, `rm`, `rmdir`, `find`, `sha256sum`, `sort`

For tool installation via `-i`:

- `node`
- `npm`

## Usage

Show help:

```bash
sudo ./agent_scope -h
```

Install Codex into `/opt/codex`:

```bash
sudo ./agent_scope -i codex
```

Install Claude Code into `/opt/claude`:

```bash
sudo ./agent_scope -i claude
```

Run Codex on a source tree:

```bash
sudo ./agent_scope -s /home/alice/projects/repo -t codex
```

Keep ACLs in place after exit so later launches can reuse them:

```bash
sudo ./agent_scope -s /home/alice/projects/repo -t codex -p
```

Run Claude Code on a source tree:

```bash
sudo ./agent_scope -s /home/alice/projects/repo -t claude
```

Resume a previous session through `agent_scope`:

```bash
sudo ./agent_scope -e 019d86eb-446f-7361-956b-aac6c08e5163
```

If more than one managed mount exists for the selected tool, also pass `-s`:

```bash
sudo ./agent_scope -e 019d86eb-446f-7361-956b-aac6c08e5163 -s /home/alice/projects/repo -t codex
```

Prepare state only without launching the tool:

```bash
sudo ./agent_scope -s /home/alice/projects/repo -t codex -n
```

Unmount one current managed bind mount interactively:

```bash
sudo ./agent_scope -d
```

Reset one tool scope completely:

```bash
sudo ./agent_scope -t codex -r
```

## Notes

- Run `agent_scope` as root, typically with `sudo`.
- By default, `-u` uses `$SUDO_USER` when available, otherwise `$USER`; `-a` defaults to `agent`.
- Managed state is stored under `/srv/work/<tool>`.
- Tool installs go to `/opt/<tool>`.
- For Codex, persistent CLI state is stored under the managed agent home at `~agent/.codex`, not inside the source tree.
- Normal exit keeps both the bind mount and ACL grant, so later runs can reuse them without repeating the recursive ACL setup.
- By default, `agent_scope` keeps the ACL backup and active ACL grant in place so later runs can skip the recursive ACL work; use `-d` or `-r` to restore and clean up persisted ACL state.
- `-p` is kept as an explicit no-op for that default behavior.
- `-d` and `-r` close managed sessions completely: they restore ACLs first and then unmount/remove managed directories, so they can take a while on very large source trees.

## Smoke Test

Run the manual smoke test:

```bash
sudo ./agent_scope_smoketest
```

Keep temporary artifacts after the test:

```bash
sudo ./agent_scope_smoketest -k
```
