# Roles

## config
- Variables: `interfaces`, `vlans`, `ntp_servers`, `banner_file`
- Templates: `interface.j2`, `vlan.j2`, `ntp.j2`, `banner.j2`
- Tasks: render configs, push with `ios_config`

## backup
- Variables: `backup_location`
- Tasks: gather running config, save timestamped file

## compliance
- Variables: `compliance_baseline_template`, `compliance_mode`
- Tasks: render baseline, diff against running, fail if drift
