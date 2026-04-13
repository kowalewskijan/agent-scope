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

For `-i codex` installation:

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

Run Codex on a source tree:

```bash
sudo ./agent_scope -s /home/alice/projects/repo -t codex
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
- By default, `-u` uses `$USER` and `-a` defaults to `agent`.
- Managed state is stored under `/srv/work/<tool>`.
- Tool installs go to `/opt/<tool>`.
- Normal exit restores ACLs but keeps the bind mount, so a later resume can reuse it.
- `-r` and `-d` are the paths that unmount managed directories.

## Smoke Test

Run the manual smoke test:

```bash
sudo ./agent_scope_smoketest
```

Keep temporary artifacts after the test:

```bash
sudo ./agent_scope_smoketest -k
```
