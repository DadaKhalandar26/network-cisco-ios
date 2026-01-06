# group_vars Directory

This directory contains example Ansible group variable files.

## 📌 Purpose

The files in `group_vars/` demonstrate how group-level variables can be structured when running Ansible **locally** or with a **static inventory**.

They are included in this repository for:

- Documentation and reference
- Local/offline Ansible testing
- Developer onboarding
- Showing expected variable structure and formatting

## 🚫 AWX / AAP Behavior

When using **AWX** or **Red Hat Ansible Automation Platform (AAP)** with **NetBox as the dynamic inventory source**, this directory is **ignored**.

### Why?

Because NetBox provides:

- Group variables (via tags, roles, sites, tenants)
- Config Contexts scoped to groups
- Device/VM metadata used for grouping

AWX/AAP does **not** load `group_vars/` from the Git repository when NetBox is the source of truth.

## 🧠 When `group_vars/` *is* used

This directory is only used when:

- Running Ansible manually with a static inventory  
- Running playbooks outside AWX/AAP  
- Explicitly loading vars using `include_vars`

Example:

```yaml
- name: Load group vars manually
  include_vars: "group_vars/all.yml"
