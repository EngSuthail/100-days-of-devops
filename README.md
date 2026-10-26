# 100 Days of DevOps

My notes from the [KodeKloud 100 Days of DevOps](https://engineer.kodekloud.com/) challenge, started October 2026.

Notes on each lab as I complete it.

> Lab credentials are deliberately left out of these notes. Secrets don't belong in a repository.

## Progress

| Day | Topic | Key concepts | Status | Notes |
|---|---|---|---|---|
| 01 | Create a user with a non-interactive shell | `useradd`, `/sbin/nologin`, service accounts, least privilege | ✅ Passed (2nd attempt) | [Day 01](day-01-nologin-user.md) |

## Lab environment

The labs run in a simulated datacenter (Stork DC). All servers are reached through a **jump host**:

```
lab terminal → jump-host → app / db / storage / backup servers
```

| Server | Hostname |
|---|---|
| App Server 1–3 | `stapp01`, `stapp02`, `stapp03` |
| Load Balancer | `stlb01` |
| Database | `stdb01` |
| Storage | `ststor01` |
| Backup | `stbkp01` |
| Mail | `stmail01` |
| Jenkins | `jenkins` |
