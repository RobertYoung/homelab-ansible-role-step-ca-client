# homelab-ansible-role-step-ca-client

Ansible role for configuring step-ca client and automatic certificate renewal on Ubuntu/Debian systems.

## Requirements

- Ansible >= 2.15
- Target: Ubuntu (focal, jammy, noble) or Debian (bullseye, bookworm)
- A running step-ca server

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `step_ca_client_url` | `https://step-ca.local.iamrobertyoung.co.uk` | URL of the step-ca server |
| `step_ca_client_fingerprint` | *(see defaults)* | Fingerprint of the step-ca root certificate |
| `step_ca_client_cert_name` | `{{ fqdn }}` | Name for the service certificate |
| `step_ca_client_cert_file` | `/etc/ssl/certs/{{ step_ca_client_cert_name }}.crt` | Path to store the certificate |
| `step_ca_client_key_file` | `/etc/ssl/private/{{ step_ca_client_cert_name }}.pem` | Path to store the private key |
| `step_ca_client_provisioner_password` | `password` | Password for the ansible provisioner |
| `step_ca_client_provisioner_password_file` | `/root/.step/config/ansible_provisioner_password.txt` | Path to store the provisioner password |

## Usage

### Install via requirements.yml

```yaml
- src: git@github.com:RobertYoung/homelab-ansible-role-step-ca-client.git
  scm: git
  version: main
  name: step_ca_client
```

```bash
ansible-galaxy install -r requirements.yml
```

### Example Playbook

```yaml
- hosts: servers
  roles:
    - role: step_ca_client
      vars:
        step_ca_client_url: https://ca.example.com
        step_ca_client_fingerprint: your-fingerprint-here
        step_ca_client_cert_name: "{{ inventory_hostname }}"
        step_ca_client_provisioner_password: "{{ vault_step_ca_password }}"
```

## What Gets Installed

- **step-cli** - Smallstep CLI tool for interacting with step-ca
- **Root CA certificate** - Bootstrapped and installed to system trust store
- **Service certificate** - Generated for the host using the ansible provisioner
- **cert-renewer systemd service/timer** - Automatic certificate renewal every 15 minutes

## License

MIT
