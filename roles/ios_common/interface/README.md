# IOS / IOS-XE Interface Automation Role

## Overview

This role manages **interface configuration for Cisco IOS and IOS-XE devices** using an **intent-based automation approach**.

It is designed to work seamlessly with:

- **NetBox** as the Source of Truth (SoT)
- **AWX / Ansible Automation Platform (AAP)** for orchestration
- **Ansible Network Collections** for device interaction

The role ensures that interface configuration on devices remains aligned with the intent defined in NetBox while supporting safe execution and rollback.

---

## Design Approach

- NetBox defines **what the interface state should be**
- Ansible defines **how the configuration is applied**
- AWX / AAP defines **when and where the job runs**
- Interface intent is data-driven
- Unused interfaces remain administratively down by default
- Rollback is supported using pre-change configuration snapshots

---

## Role Structure

    roles/
    └── ios_common/
        └── interface/
            ├── README.md
            └── tasks/
                ├── precheck.yml
                ├── apply.yml
                ├── verify.yml
                └── rollback.yml


## Task Details

### 1. `precheck.yml` – Validation & Preparation

Purpose:
- Normalize NetBox inventory data provided by the AWX inventory plugin
- Validate that interface intent exists
- Capture the current running configuration for rollback

Key behavior:
- Converts `config_context` into clean variables:
  - `interfaces`
  - `interface_defaults`
- Stops execution if no interface intent is present

---

### 2. `apply.yml` – Apply Interface Configuration

Purpose:
- Apply interface configuration based on NetBox intent

Supported configuration:
- Interface enable / disable
- Description
- MTU
- Speed and auto-negotiation handling
- Duplex
- Routed interface configuration
- IPv4 address assignment
- Link-status logging

The task is idempotent and applies only what is defined in the intent.

---

### 3. `verify.yml` – Post-Change Verification

Purpose:
- Verify interface status after configuration changes
- Display operational output for validation and review

This task does not modify device configuration.

---

### 4. `rollback.yml` – Configuration Rollback

Purpose:
- Restore interface configuration captured during `precheck.yml`

Notes:
- Rollback depends on a successful precheck run
- Intended for use in AWX / AAP workflows

---

## NetBox Integration

This role expects interface intent to be supplied by **NetBox Config Contexts** and synchronized into AWX using the NetBox inventory plugin.

### Example NetBox Config Context (JSON)

    {
      "interface_defaults": {
        "enabled": false,
        "description": "Unused - Admin Down"
      },
      "interfaces": [
        {
          "name": "GigabitEthernet1",
          "description": "Primary active interface",
          "enabled": true,
          "mode": "routed",
          "mtu": 1500,
          "speed": "auto",
          "duplex": "auto",
          "logging_event_link": true,
          "ipv4": {
            "address": "192.168.29.4",
            "mask": "255.255.255.0"
          }
        }
      ]
    }

# Variables

| Variable             | Description                                       |
| -------------------- | ------------------------------------------------- |
| `interfaces`         | List of interfaces defined in NetBox              |
| `interface_defaults` | Default interface behavior                        |
| `config_context`     | Raw NetBox inventory data (normalized internally) |


#### Supported Platforms
Cisco IOS
Cisco IOS-XE

Example Playbook

    - name: Apply interface configuration
      hosts: ios_xe_devices
      gather_facts: no
      connection: network_cli

      roles:
        - role: ios_common/interface
          tags: [interface]

## AWX / AAP Usage

#### Recommended usage with AWX or Ansible Automation Platform:
- Inventory sourced from NetBox
- config_context: true enabled in inventory plugin
- Job templates scoped using:
- platforms
- device roles
- tags
- Optional use of check mode before applying changes
- Rollback executed via workflows if required

## Notes & Best Practices
- Do not store CLI commands in NetBox
- Do not hardcode interface configuration in playbooks
- Always run precheck.yml before applying changes
- Avoid using local_context_data; use merged config_context
- Treat NetBox as the single source of interface intent

#### Future Improvements

SVI (VLAN interface) configuration support

Drift detection and compliance checks

Extended platform support

Integration with approval-based AWX workflows


