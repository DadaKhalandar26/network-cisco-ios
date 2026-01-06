# host_vars Directory

This directory contains example Ansible host-specific variable files.

## 📌 Purpose

The files in `host_vars/` show how per-host variables can be structured when running Ansible **locally** or with a **static inventory**.

They are included for:

- Documentation and reference
- Local/offline testing
- Developer examples
- Demonstrating expected variable schema

## 🚫 AWX / AAP Behavior

When using **AWX** or **Red Hat Ansible Automation Platform (AAP)** with **NetBox as the dynamic inventory source**, this directory is **ignored**.

### Why?

Because NetBox provides:

- Device-level Config Contexts
- Device/VM custom fields
- Metadata that AWX/AAP converts into host variables

AWX/AAP does **not** read `host_vars/` from the Git repository when NetBox is the source of truth.

## 🧠 When `host_vars/` *is* used

This directory is only used when:

- Running Ansible manually with a static inventory  
- Running playbooks outside AWX/AAP  
- Explicitly loading vars using `include_vars`

Example:

```yaml
- name: Load host vars manually
  include_vars: "host_vars/{{ inventory_hostname }}.yml"
