# Cisco IOS Configuration Backup to SFTP using AWX

This repository provides an automated solution to back up Cisco IOS running configurations using Ansible and AWX, and securely store them on an SFTP server.
The implementation is designed for AWX execution environments and supports chrooted SFTP servers.

# Features
- Automated backup of Cisco IOS running configurations
- Timestamped backup files per device
- Secure upload to SFTP server
- Compatible with AWX / Ansible Automation Platform
- Parallel execution across multiple devices
- Automatic cleanup of temporary backup files
- Secure credential handling via AWX

# Architecture Overview
Cisco IOS Devices
→ AWX Execution Environment
→ SFTP Server (Chrooted)

Real storage path on SFTP server:
/netbackups/ios/backups/

# Backup File Naming Convention
Each backup file is saved using the format:
<inventory_hostname>-<backup_timestamp>.cfg

Example:
Test-IOS-01-20251220-101001.cfg

# Repository Structure
.
├── playbooks/
│   └── backup.yml
├── roles/
│   └── backup/
│       └── tasks/
│           └── main.yml
├── group_vars/
│   └── all.yml
└── README.md

# Prerequisites
## Cisco IOS Devices
- SSH enabled
- Local user with privilege 15
- login local configured under VTY lines

## AWX / Ansible
AWX or Ansible Automation Platform installed
Execution Environment includes:
- cisco.ios collection
- expect module

## SFTP Server
- SFTP enabled via OpenSSH
- ChrootDirectory configured
- Writable directory inside chroot
Example chroot configuration:
ChrootDirectory /netbackups

## Credential Management in AWX
This solution requires multiple credentials attached to the same Job Template.

# Machine Credential (Cisco IOS Access)
Used by AWX to connect to Cisco IOS devices.

- Credential Type: Machine
- Username: Cisco device username
- Password: Cisco device password
- Privilege Escalation: Enabled
- Become Method: enable
- Become Password: Cisco enable password

## Custom Credential Type (SFTP)
Create a custom credential type with the following injected variables:
- sftp_host
- sftp_username
- sftp_password

These credentials are used only for uploading backup files to the SFTP server.

# Backup Workflow
## Step 1: Backup Running Configuration
The running configuration is collected from each Cisco IOS device and stored temporarily on the AWX execution node.
/tmp/<hostname>-<timestamp>.cfg

## Step 2: Upload Backup to SFTP Server
The backup file is uploaded to the SFTP server using a non-interactive SFTP session.
Target directory (inside SFTP session):
/ios/backups/

Mapped filesystem path:
/netbackups/ios/backups/

## Step 3: Cleanup Temporary File
After successful upload, the temporary file is removed from the AWX execution node to prevent disk buildup.

# Backup Task Implementation
visit roles/backup/main.yml

# Verification
Verify on SFTP Server (Root User)
ls -l /netbackups/ios/backups
less /netbackups/ios/backups/Test-IOS-01-20251220-101001.cfg
## Verify via SFTP
sftp backupuser@<server-ip>
cd /ios/backups
ls -l

# Security Considerations
- No credentials are hardcoded in playbooks
- All secrets are stored securely in AWX
- SFTP access is restricted using chroot
- SSH host key checking is disabled only within isolated automation jobs

# Known Limitations
- Uses SFTP interactive mode (expect)
- Requires SFTP write permissions to be configured correctly
- Chroot path must be mapped properly on the server

# Future Enhancements
- Per-device backup directories
- Date-based backup folders
- Backup retention policy
- Compressed backups
- Restore-from-backup playbook
- SCP-based transfer

# Author
Bandar Shaik Dada Khalandar
Senior Network Engineer
