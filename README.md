# Ansible Azure VM Baseline

![Lint](https://github.com/Kishore2488/ansible-azure-vm-baseline/actions/workflows/lint.yml/badge.svg)

An Ansible role that applies the baseline hardening and housekeeping I'd run against any new Azure VM before it joins a fleet: unattended security updates, SSH hardening, a firewall default-deny posture, fail2ban, and consistent timezone.

## Structure

```
site.yml                     # entry-point playbook
inventory/hosts.ini           # sample inventory
requirements.yml              # collection dependencies (community.general)
roles/baseline/
  tasks/main.yml               # the hardening tasks
  defaults/main.yml            # overridable defaults (SSH port, allowed ports, etc.)
  handlers/main.yml            # service restarts triggered by config changes
```

## Run it

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook -i inventory/hosts.ini site.yml
```

## What the role does

- Enables `unattended-upgrades` so security patches land without a manual cron job someone forgets to set up.
- Hardens `sshd_config`: disables root login and password auth, enforces key-only access.
- Installs and enables `fail2ban` against SSH brute-force attempts.
- Sets UFW to default-deny inbound, explicitly allowing only SSH and whatever ports the caller passes in via `baseline_allowed_ports`.
- Syncs timezone so log timestamps across a fleet are comparable.

## CI

`yamllint` and `ansible-lint` run on every push — this catches the kind of mistake (a task missing `name:`, a module used without its fully-qualified collection name, risky file permissions) that's easy to miss in review but easy for a linter to catch.
