# Ansible N8N Deployment

This repository contains Ansible playbooks to deploy n8n stacks with a HA.
Once started, you will have a docker on your server with 3 n8n container and 6 redis container.

## Requiered
- A postgres database (I recommand suppabase).
- A server with SSH.

## Get Started

### 1. Clone the project

```bash
git clone https://github.com/your-username/ansible-n8n.git
cd ansible-n8n
```

### 2. Create a virtual environment and install dependencies

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 3. Configure n8n variables

Create a `n8n_servers.yml` file in the `group_vars` directory. You can copy the default variables and adapt them to your needs.

```bash
cp roles/n8n_stacks/defaults/main.yml group_vars/n8n_servers.yml
```

Now, edit `group_vars/n8n_servers.yml` with your own variables.

It is strongly recommended to encrypt this file with Ansible Vault. Create a `vault_password.txt` file containing your password, then run:

```bash
ansible-vault encrypt group_vars/n8n_servers.yml --vault-password-file vault_password.txt
```

### 4. Configure SSH access

Make sure you can connect to your server via SSH. You can either configure an SSH key in your `~/.ssh/config` file, or specify the connection details directly in the `inventory.yml` file:

```yaml
n8n_servers:
  hosts:
    my-n8n-server:
      ansible_host: your_server_ip
      ansible_user: your_ssh_user
      ansible_ssh_private_key_file: /path/to/your/private/key
```

### 5. Run the playbook

You can now deploy the n8n stack by running the main playbook:

```bash
ansible-playbook -i inventory.yml main.yml --vault-password-file vault_password.txt
```

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