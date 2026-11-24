---
title: "Fixing a failed systemd service (troubleshooting procedure)"
description: "A repeatable process for diagnosing and fixing a failed systemd service."
pubDatetime: 2026-10-12T09:00:00Z
category: linux
tags:
  - linux
  - systemd
draft: false
---

## Context

This procedure is a starting point for investigating a Linux service that
exits during startup. Replace `example.service` with the affected unit and
adapt any corrective action to the service's actual configuration.

## Symptoms

- `systemctl status` reports the unit as `failed`.
- A dependent application is unavailable.
- The service may repeatedly restart or exit with a non-zero status.

## Diagnosis

Check the unit state, recent journal messages, and the effective unit
configuration before changing anything:

```bash
sudo systemctl status example.service --no-pager
sudo journalctl -u example.service -b --no-pager -n 100
sudo systemctl cat example.service
```

Look for the first meaningful error, such as a missing executable, invalid
configuration, unavailable dependency, or permission problem. Confirm that the
referenced files exist and that the service account can read or execute them.

## Fix

Correct the specific issue identified in the logs. For example, if the unit
references an incorrect executable path, edit the unit override rather than
modifying a package-managed unit file:

```bash
sudo systemctl edit example.service
```

Add a corrected `ExecStart` setting to the override, save it, and reload the
unit definitions. Do not copy this example path without confirming the actual
service executable.

```bash
sudo systemctl daemon-reload
sudo systemctl restart example.service
```

## Verification

Confirm that the service remains active and that its latest logs no longer
show the original failure:

```bash
sudo systemctl is-active example.service
sudo systemctl status example.service --no-pager
sudo journalctl -u example.service -b --no-pager -n 30
```

If it fails again, return to the new journal output instead of repeatedly
restarting it.
