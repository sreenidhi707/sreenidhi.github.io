---
layout: post
title: "SSH Key Permissions That Avoid Authentication Failures"
summary: "The permission settings SSH expects for private keys, public keys, and the ~/.ssh directory, plus the reasoning behind each mode."
tags: [ssh, security, linux]
---

SSH is intentionally strict about key file permissions. If your private key or `~/.ssh` directory is too open, SSH may refuse to use the key because another local user could read or modify it.

The fix is usually simple: make the directory private, keep the private key readable only by your user, and allow the public key to be readable.

## Recommended Permissions

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

These modes mean:

- `700` for `~/.ssh`: only your user can read, write, and enter the directory.
- `600` for `~/.ssh/id_rsa`: only your user can read and write the private key.
- `644` for `~/.ssh/id_rsa.pub`: your user can write the public key, and other users can read it.

If you use a different key type, such as `id_ed25519`, apply the same private-key permission:

```bash
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

## Why SSH Cares

A private key is an authentication secret. If it is readable by other users on the same machine, they may be able to authenticate as you anywhere that key is trusted.

OpenSSH protects against that by checking permissions before using identity files. When the permissions are too broad, you may see an error such as:

```text
WARNING: UNPROTECTED PRIVATE KEY FILE!
Permissions 0644 for 'id_rsa' are too open.
This private key will be ignored.
```

## Check the Current Modes

Use `ls -ld` for the directory and `ls -l` for the key files:

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/id_rsa ~/.ssh/id_rsa.pub
```

The first column shows the mode. For example:

```text
drwx------  ~/.ssh
-rw-------  id_rsa
-rw-r--r--  id_rsa.pub
```

## Also Check Ownership

Permissions are only half of the story. The files should also be owned by your user:

```bash
chown -R "$USER":"$USER" ~/.ssh
```

On a personal Linux machine this is usually fine. On shared or managed systems, confirm the expected owner and group before changing ownership recursively.

## A Quick Repair Sequence

When SSH key permissions are messy, this sequence usually gets the local client back to a healthy baseline:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chown "$USER":"$USER" ~/.ssh ~/.ssh/id_rsa ~/.ssh/id_rsa.pub
```

After that, retry SSH with verbose output if it still fails:

```bash
ssh -v user@example.com
```

Verbose mode shows which keys SSH tried and whether it ignored one because of permissions.
