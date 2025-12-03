# AWX Workflows

## Validate → Backup → Apply → Rollback
1. **Validate**: Runs `playbooks/validate.yml` in check mode.
2. **Backup**: Runs `playbooks/backup.yml` to snapshot configs.
3. **Apply (Pilot)**: Runs `playbooks/site.yml` with `serial: 1`.
4. **Approval Step**: Manual approval in AWX.
5. **Apply (Full)**: Runs `playbooks/site.yml` with `serial: 50%`.
6. **Rollback**: Runs `playbooks/rollback.yml` if apply fails.
