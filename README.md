# Ansible N8N Deployment

This repository contains Ansible playbooks to deploy n8n stacks.

## Structure

```
ansible-n8n/
├── inventory.yml             # Defines your target servers
├── main.yml                  # The main playbook that orchestrates everything
├── group_vars/
│   └── n8n_servers.yml       # Encrypted variables for the n8n servers
├── roles/
│   ├── common/               # Role to prepare the server (install Docker, etc.)
│   │   └── tasks/
│   │       └── main.yml
│   └── n8n_stacks/           # Role to deploy the n8n stack
│       ├── defaults/
│       │   └── main.yml
│       └── tasks/
│           └── main.yml
└── templates/
    ├── docker-compose.yml.j2 # Template for Docker Compose
    └── env.j2                # Template for the environment variables
```

## Useful Ansible Commands

### Vault Encryption

Ansible Vault is used to encrypt files containing sensitive data, like `group_vars/n8n_servers.yml`.

- **Edit an encrypted file:**
  ```bash
  ansible-vault edit group_vars/n8n_servers.yml --vault-password-file vault_password.txt
  ```

- **Encrypt a new file:**
  ```bash
  ansible-vault encrypt group_vars/n8n_servers.yml
  ```

- **Decrypt a file:**
  ```bash
  ansible-vault decrypt group_vars/n8n_servers.yml --vault-password-file vault_password.txt
  ```

- **Change a vault's password:**
  ```bash
  ansible-vault rekey group_vars/n8n_servers.yml
  ```

### Playbook Execution

- **Deploy the full configuration:**
  ```bash
  ansible-playbook -i inventory.yml main.yml --vault-password-file vault_password.txt
  ```
  You will be prompted for the vault password.

- **Run only specific tasks (using tags):**
  ```bash
  # Example if tags are defined in the roles
  ansible-playbook -i inventory.yml main.yml --tags "setup,deploy"
  ```

- **Check the playbook syntax:**
  ```bash
  ansible-playbook -i inventory.yml main.yml --syntax-check
  ```

### Inventory Management

- **List hosts:**
  ```bash
  ansible -i inventory.yml --list-hosts all
  ```

- **Run a simple command on all servers (ping):**
  ```bash
  ansible -i inventory.yml all -m ping
  ```