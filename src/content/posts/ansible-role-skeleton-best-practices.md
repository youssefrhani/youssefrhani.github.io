---
title: "Ansible role skeleton and best practices"
description: "A small, maintainable Ansible role structure with sensible defaults and verification."
pubDatetime: 2026-10-19T09:00:00Z
category: ansible
tags:
  - ansible
  - automation
draft: false
---

## Context

Roles make automation easier to reuse and review by separating defaults,
tasks, handlers, and templates. This example shows a small role named
`web_service`; replace the example values with those for your service.

## Symptoms

- Playbooks repeat the same tasks with slightly different values.
- Variables and service configuration are difficult to find.
- A change to one playbook unexpectedly affects another.

## Diagnosis

Review the current playbook and identify which tasks are reusable, which
values differ by environment, and which changes require a handler. Keep
environment-specific values in inventory rather than hard-coding them in
tasks.

## Fix

Use a role structure that separates variables from implementation:

```text
roles/web_service/
  defaults/main.yml
  handlers/main.yml
  tasks/main.yml
```

Define safe, overridable defaults:

```yaml
---
web_service_package: nginx
web_service_name: nginx
web_service_state: started
```

Keep tasks idempotent and notify a handler only when the package changes:

```yaml
---
- name: Install the web service package
  ansible.builtin.package:
    name: "{{ web_service_package }}"
    state: present
  notify: Restart web service

- name: Ensure the web service is enabled and running
  ansible.builtin.service:
    name: "{{ web_service_name }}"
    enabled: true
    state: "{{ web_service_state }}"
```

Declare the handler in `handlers/main.yml`:

```yaml
---
- name: Restart web service
  ansible.builtin.service:
    name: "{{ web_service_name }}"
    state: restarted
```

Use FQCNs, descriptive task names, and role defaults that can be overridden
through inventory. Add templates and tests when the role's scope grows.

## Verification

Run syntax validation and a check-mode play against a non-production target
before applying changes:

```bash
ansible-playbook --syntax-check site.yml
ansible-playbook --check --diff -i inventory site.yml
```

Review the planned changes, then run normally and verify the service is
enabled and active on the target.
