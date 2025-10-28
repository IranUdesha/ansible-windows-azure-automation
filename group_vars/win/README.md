Shared variables for hosts in the `ubuntu` inventory group.

Recommended pattern for service principal and other shared settings:

- Keep non-sensitive, shared config in `azure.yml` (checked in):
  - Example keys: `azure_subscription_id`, `azure_use_msi: false`
- Keep secrets (client secret, tenant ID, client ID) in `vault.yml` encrypted with Ansible Vault (NOT checked in plaintext):
  - `azure_tenant_id`
  - `azure_client_id`
  - `azure_client_secret`

Create/update the encrypted vault file:

1) Create or edit:
   ansible-vault edit group_vars/ubuntu/vault.yml

2) Or encrypt an existing yaml:
   ansible-vault encrypt group_vars/ubuntu/vault.yml

Use the variables in roles/playbooks directly; group_vars are auto-loaded based on inventory group membership.

Note: Do not commit vault passwords; configure `vault_password_file` locally (and keep it out of git).
