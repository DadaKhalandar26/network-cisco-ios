# network-cisco-ios

This repository provides production-ready network automation for Cisco IOS devices, designed to run on AWX / Ansible Automation Platform with enterprise change-management practices.

The solution supports safe configuration changes, pre-change backups, approval-based workflows, automatic rollback, configuration drift handling, and audit evidence generation.

# Scope

This repository is for Cisco IOS only.

Supported capabilities:

- Cisco IOS configuration backups (SFTP)
- Controlled configuration changes (VLANs, interfaces, banners)
- Manual and automatic rollback
- Configuration drift detection and remediation
- CRQ / Change ID–based traceability
- AWX workflow approvals (CAB / Change Management)
- Audit and diff capture for compliance

# Repository Structure
        network-automation-cisco-ios/
        ├── README.md
        │
        ├── inventories/
        │   ├── netbox_inventory.yml
        │   └── example.yml
        │
        ├── playbooks/
        │   ├── ios_common/
        │   │   ├── backup.yml
        │   │   ├── restore.yml
        │   │   ├── banner.yml
        │   │   ├── baseline.yml
        │   │   ├── vlan_apply.yml
        │   │   ├── vlan_manual_rollback.yml
        │   │   ├── interface_apply.yml
        │   │   ├── interface_manual_rollback.yml
        │   │   ├── drift_detect.yml
        │   │   ├── drift_remediate.yml
        │   │   └── compliance_check.yml
        │   │
        │   └── ios_xe_only/
        │       ├── upgrade.yml
        │       ├── install_mode.yml
        │       ├── restconf_enable.yml
        │       └── netconf_enable.yml
        │
        ├── roles/
        │   ├── ios_common/
        │   │   ├── backup/
        │   │   │   └── tasks/main.yml
        │   │   │
        │   │   ├── vlan/
        │   │   │   └── tasks/
        │   │   │       ├── main.yml
        │   │   │       ├── precheck.yml
        │   │   │       ├── apply.yml
        │   │   │       ├── verify.yml
        │   │   │       ├── rollback.yml
        │   │   │       └── audit.yml
        │   │   │
        │   │   ├── interface/
        │   │   │   └── tasks/
        │   │   │       ├── precheck.yml
        │   │   │       ├── apply.yml
        │   │   │       ├── verify.yml
        │   │   │       └── rollback.yml
        │   │   │
        │   │   ├── drift/
        │   │   │   └── tasks/
        │   │   │       ├── capture.yml
        │   │   │       ├── compare.yml
        │   │   │       └── remediate.yml
        │   │   │
        │   │   └── compliance/
        │   │       └── tasks/
        │   │           ├── check.yml
        │   │           └── fix.yml
        │   │
        │   ├── ios_xe_only/
        │   │   ├── upgrade/
        │   │   │   └── tasks/
        │   │   │       ├── precheck.yml
        │   │   │       ├── install.yml
        │   │   │       ├── verify.yml
        │   │   │       └── rollback.yml
        │   │   │
        │   │   ├── restconf/
        │   │   └── netconf/
        │   │
        │   └── common/
        │       ├── rollback/
        │       │   └── tasks/main.yml
        │       └── audit/
        │           └── tasks/main.yml
        │
        ├── templates/
        │   ├── vlans.j2
        │   ├── interfaces.j2
        │   └── banner.j2
        │
        ├── group_vars/
        │   ├── ios.yml
        │   └── ios_xe.yml
        │
        ├── vars/
        │   ├── global.yml
        │   ├── ios.yml
        │   └── ios_xe.yml
        │
        ├── device_lists/
        │   ├── change_1234.yml
        │   └── rollback_1234.yml
        │
        ├── docs/
        │   ├── awx-workflows.md
        │   ├── rollback-policy.md
        │   ├── audit-policy.md
        │   └── ios-vs-ios-xe.md
        │
        └── .gitignore

# Design Principles
- Playbooks define intent
- Roles contain device logic
- AWX workflows enforce governance
- Rollback always restores a known-good state
- Every change produces audit evidence

This structure aligns with enterprise automation and ITIL-based change management.

# Role Execution Model

Roles are executed using tasks/main.yml as the controller file.

Example for the VLAN role:

roles/vlan/tasks/main.yml

- import_tasks: precheck.yml
- import_tasks: apply.yml
- import_tasks: verify.yml
- import_tasks: audit.yml

Rollback logic is intentionally separated and executed only via:

- A dedicated rollback playbook
- An AWX workflow failure path
- Manual operator action

# Playbook Usage
Apply VLAN Configuration

playbooks/vlan_apply.yml

Rollback VLAN Configuration

playbooks/vlan_rollback.yml

# Backup and Audit Storage

- Pre-change backups stored on SFTP
- Audit diffs generated per device
- Chrooted SFTP environments supported
- Files are tagged using hostname and CRQ ID

Example:

Test-IOS-01-CRQ-2025-00123-pre.cfg

Test-IOS-01-CRQ-2025-00123-vlan-diff.txt

# Configuration Drift Management

- Drift detection compares running configuration with intended state
- Drift remediation requires approval
- Drift changes follow the same rollback and audit process

# Security Considerations
- No credentials stored in Git
- All secrets managed in AWX credentials
- Device access via Machine credentials
- Backup access via custom SFTP credentials
- Rollback restores full configuration state (will work on more to optimize it.)

# Supported Execution Platform
- AWX
- Ansible Automation Platform
- Cisco IOS devices (SSH / network_cli)

# Intended Audience
- Network Engineers
- Automation Engineers
- NOC / Operations Teams

# Author
Bandar Shaik Dada Khalandar

Senior Network Engineer

# License

This project is licensed under the Eclipse Public License v2.0 (EPL-2.0).

You are free to use, modify, and distribute this software in compliance with the terms of the EPL-2.0 license.

See the LICENSE file for full license details.
