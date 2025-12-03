# Network Automation Core Project

This repository contains Ansible playbooks, roles, and templates for managing
Cisco IOS devices as part of the Network Automation Core Project led by
B S Dada Khalandar.

## Key Features
- Modular roles for config, backup, and compliance
- NetBox dynamic inventory integration
- AWX workflows for validate → backup → apply → rollback
- GitHub Actions CI/CD for linting, testing, and EE builds

## Getting Started
1. Install Ansible 2.16+
2. Configure NetBox token in environment
3. Run `ansible-playbook -i inventories/netbox_inventory.yml playbooks/validate.yml`
