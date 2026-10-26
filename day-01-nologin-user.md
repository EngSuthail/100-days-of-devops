# Day 01: Create a User with a Non-Interactive Shell

## Task

A backup agent tool needs its own user account. Create a user named `anita` with a non-interactive shell on the specified app server.

## Concepts

### Every action on Linux belongs to a user
Every process runs as a user, and every file is owned by a user. When the kernel runs a program, it tags the process with that user's **UID** and checks every file access against it. So a program like a backup agent needs an identity to run under.

### Login shell
The program that starts when a user logs in. It is stored in the last field of the user's line in `/etc/passwd`:

```
anita:x:1002:1002::/home/anita:/sbin/nologin
```

Fields (separated by `:`): username, password placeholder, UID, GID, comment, home directory, login shell.

- `/bin/bash`: an interactive shell, so the user gets a command prompt.
- `/sbin/nologin`: prints "This account is currently not available" and exits, so **nobody can log in** as this user.

The shell is only used at login time. A `nologin` account can still own files and run processes.

### Service accounts
Accounts for **programs, not people** (for example `mysql`, `nginx`, `jenkins`, a backup agent). They need an identity so file permissions work, but no human should ever log in as them.

### Jump host (bastion)
The single, locked-down entry point into a private network. You SSH into the jump host first, then from there into the other servers. One exposed machine is easier to secure and audit than many.

## Commands

```bash
whoami                                # which user am I?
hostname                              # which machine am I on?
ssh steve@stapp02                     # log into App Server 2 as steve
sudo useradd -s /sbin/nologin anita   # create anita with a no-login shell (as root)
grep anita /etc/passwd                # verify: the line should end in /sbin/nologin
exit                                  # go back to the jump host
```

| Part | Meaning |
|---|---|
| `ssh user@host` | Open an encrypted remote terminal on another machine |
| `sudo` | Run one command as root; asks for the *current* user's password |
| `useradd` | Create a new user |
| `-s` | Set the login **s**hell |
| `grep PATTERN FILE` | Show lines in FILE that contain PATTERN |
| `Ctrl + C` | Cancel a running or stuck command |

Notes:
- On the first SSH connection, answer `yes` to the fingerprint question. SSH remembers the server's identity after that.
- Passwords don't appear on screen while typing. That's normal.
- No output after a command usually means success.

## Mistakes and lessons

1. **Missing space.** I typed `grep anita/etc/passwd`. grep treated the whole thing as the search text, had no file to read, and waited for keyboard input.
   **Lesson:** spaces separate arguments. If a command hangs, press `Ctrl + C` and check the spacing.
2. **Wrong server.** I created the user on App Server 2, but the checker expected App Server 3. Passed on the redo.
   **Lesson:** in a datacenter of near-identical servers, the classic mistake is doing the right thing on the wrong machine. Re-read the target server and check the prompt (`user@hostname`) before acting.

## Interview Q&A

**Q: Why not give the backup user a normal bash shell and a strong password? Isn't that good enough?**

> No, a strong password alone isn't enough, because passwords can leak. If the backup account had a bash shell, a leaked password would give an attacker a working terminal on the server, with access to everything the backup user can read. The backup agent never needs a human to log in as it, so giving it a shell is more privilege than its job requires. With nologin, the account still works for the program, but even a leaked password gives an attacker no way in. It's least privilege plus defence in depth.

Key ideas:
- **Least privilege:** give each account only what its job needs.
- **Defence in depth:** don't rely on one protection. The password is one layer; `nologin` still protects you when the password fails.
- **Smaller attack surface:** only real human accounts accept logins.
- **Accountability:** keeping program and human accounts separate keeps logs meaningful.
