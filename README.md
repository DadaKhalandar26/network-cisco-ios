# network-automation-cisco-ios

This repository provides production-ready network automation for Cisco IOS and Cisco IOS-XE platforms, designed to run on AWX / Ansible Automation Platform and aligned with enterprise change-management and governance practices.

The solution enables safe, auditable, and controlled network changes by combining pre-change validation, automated backups, approval-based workflows, rollback mechanisms, configuration drift handling, and audit evidence generation.

The repository is designed to be future-proof, allowing additional Cisco IOS-XE capabilities to be introduced without restructuring core automation logic.

# Scope

This repository supports Cisco IOS and Cisco IOS-XE devices.

The design treats IOS-XE as a modern evolution of IOS, sharing a common automation baseline while isolating IOS-XE-specific capabilities to dedicated workflows and roles.

### Supported Capabilities
- Cisco IOS / IOS-XE configuration backups (SFTP-based)
- Controlled configuration changes:
        - VLANs
        - Interfaces
        - Banners
        - Baseline configurations
- Manual and automatic rollback mechanisms
- Configuration drift detection and remediation
- CRQ / Change ID–based traceability
- AWX workflow approvals (CAB / Change Management)
- Audit logging and configuration diff capture for compliance
- Platform-aware execution for IOS-XE–specific features (upgrade, NETCONF, RESTCONF)

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
- **Intent-based automation** using Ansible playbooks
- **Reusable, modular roles** aligned with Ansible best practices
- **Controller-driven execution** using AWX or ansible Automation platform workflows
- **Separation of intent, logic, and execution**
- **Idempotent configuration management**
- **Deterministic rollback and remediation paths**
- **Source-of-Truth–aligned inventory design**
- **Audit-first operational model**

This design aligns with enterprise automation architectures, including controller-based automation, modular content design, and lifecycle-based operations.

# Role Execution Model

Roles use tasks/main.yml as the orchestration entry point.

Example – VLAN Role
        
        import_tasks: precheck.yml
        import_tasks: apply.yml
        import_tasks: verify.yml
        import_tasks: audit.yml

Rollback logic is intentionally excluded from the default execution path and is triggered only through:

- A dedicated rollback playbook
- An AWX or Ansible Automation platform workflow failure path
- Explicit manual operator action

This prevents unintended rollback execution during normal change workflows.

# Playbook Usage
Apply VLAN Configuration

        playbooks/vlan_apply.yml

Manual VLAN Rollback

        playbooks/vlan_manual_rollback.yml

IOS-XE Platform Operations

        playbooks/ios_xe_only/upgrade.yml

# Backup and Audit Storage
- Pre-change backups stored on SFTP servers
- Audit diffs generated per device and change
- Chrooted SFTP environments supported
- Files tagged using hostname and CRQ ID
Example

        Test-IOS-01-CRQ-2025-00123-pre.cfg
        Test-IOS-01-CRQ-2025-00123-vlan-diff.txt

# Configuration Drift Management

- Drift detection compares running configuration with intended state
- Drift remediation requires approval
- Drift changes follow the same rollback and audit process

# Security Considerations
- No credentials stored in Git
- All secrets managed via AWX credentials
- Device access via AWX Machine credentials
- Backup access via dedicated SFTP credentials
- Rollback restores known configuration state (continuous optimization planned)

# Supported Execution Platform
- AWX
- Ansible Automation Platform
- Cisco IOS and IOS-XE devices (SSH / network_cli)

# Intended Audience
- Network Engineers
- Automation Engineers
- NOC / Operations Teams
- Platform and Infrastructure Engineers

# Author
### **Bandar Shaik Dada Khalandar**

Senior Network Engineer

# License

This project is licensed under the Eclipse Public License v2.0 (EPL-2.0).

You are free to use, modify, and distribute this software in compliance with the terms of the EPL-2.0 license.

See the LICENSE file for full license details.
