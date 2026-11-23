---
title: Syncfiles
tags:
  - deployment
---

Each OS deployment profile has a `syncfiles` file at `/var/lib/confluent/public/os/<profilename>/syncfiles`. Confluent
uses it to copy or merge files from the deployment server to deployed nodes over SSH and `rsync`.

`rsync` must be installed on the confluent server, and the automation SSH key must be set up with `osdeploy initialize -a`.

## Basic syntax

Each non-comment line names a source path on the deployment server and an optional destination path separated by `->`.
Omitting `->` copies to the same path on the node. Prefixing the destination with `<noderange>:` limits the entry to
matching nodes.

```text
# Copy to a different destination
/etc/confluent/sync/motd -> /etc/motd

# Copy to the same path on the node
/etc/hosts

# Limit to a noderange
/etc/confluent/sync/slurm-gpu.conf -> gpu:/etc/slurm/slurm.conf

# Multiple sources or globs require a trailing slash on the destination
/etc/confluent/sync/conf.d/*.conf -> /etc/myapp/conf.d/

# Include another synclist file
</etc/confluent/sync/common.syncfiles>
```

Naming a directory as the source copies that directory recursively, including subdirectories and empty directories:

```text
/etc/confluent/sync/munge -> /etc/munge
```

Relative paths in `<...>` includes resolve relative to the directory of the current synclist file. Lines starting with `#`
are comments.

## Sections

A section header applies to all following lines until the next section header:

- `REPLACE:` is the default and overwrites the destination file.
- `APPENDONCE:` appends the source file to the destination only if the destination does not already contain the exact
  source content.
- `MERGE:` merges `/etc/passwd`, `/etc/group`, and `/etc/hosts`. For `/etc/passwd` and `/etc/group`, confluent merges
  non-system UIDs and GIDs based on `/etc/login.defs` and adds locked entries to `/etc/shadow` or `/etc/gshadow` as
  needed. For `/etc/hosts`, matching IPs or hostnames are updated in place and new entries are appended.

```text
APPENDONCE:
/etc/confluent/sync/limits.conf -> /etc/security/limits.conf

MERGE:
/etc/passwd
/etc/group
/etc/hosts
```

## Permissions and ownership

Confluent reads source files as the `confluent` user, not `root`. Every source file and its parent directories must be
accessible to `confluent`. You can test access with `sudo -u confluent bash`.

Do not store sensitive source files under `/var/lib/confluent/public/`, because that directory is served without
authentication. Place them elsewhere on the server with permissions readable by `confluent`.

By default, `rsync` preserves permissions, ownership, timestamps, and ACLs. Files owned by the `confluent` daemon user
or group are mapped to `root:root` on the target node.

To override ownership or file mode for an entry, append options in parentheses with octal permissions:

```text
/etc/confluent/sync/munge.key -> compute:/etc/munge/munge.key (owner=munge,group=munge,permissions=400)
```

## Running syncfiles

Confluent runs `syncfiles` automatically during OS deployment (`post` and `onboot`). To re-run the `syncfiles` of the
currently deployed profile on running nodes, use [`nodeapply`](../manuals/nodeapply.md):

```bash
nodeapply -F <noderange>
```
