# Ansible Role: Forgejo (Ludus)

An Ansible Role that installs and configures **Forgejo** (the open-source forge powering Codeberg) on Debian/Ubuntu Linux hosts inside a [Ludus](https://docs.ludus.cloud) range.

## Requirements

A Ludus-compatible template must be running Debian 11/12 or Ubuntu 20.04/22.04.

## Role Variables

```yaml
# Forgejo version to install
ludus_forgejo_version: "14.0.3"
ludus_forgejo_arch: "amd64"

# System user that runs the service
ludus_forgejo_user: "git"
ludus_forgejo_group: "git"

# Network settings
ludus_forgejo_domain: "localhost"
ludus_forgejo_http_port: 3000
ludus_forgejo_ssh_port: 2222
ludus_forgejo_root_url: "http://{{ ludus_forgejo_domain }}:{{ ludus_forgejo_http_port }}/"
ludus_forgejo_disable_ssh: false

# Database (sqlite3 | mysql | postgres)
ludus_forgejo_db_type: "sqlite3"

# Bootstrap admin account on first install
ludus_forgejo_create_admin: true
ludus_forgejo_admin_user: "ludusadmin"
ludus_forgejo_admin_password: "ForgejoPW1!"
ludus_forgejo_admin_email: "admin@localhost"

# Set to true + configure domain to drop an nginx reverse-proxy config
ludus_forgejo_setup_nginx: false

# IMPORTANT — change these secrets before deploying to any network
ludus_forgejo_secret_key: "ChangeMeToSomethingRandom64CharactersLong00000000000000000000000"
ludus_forgejo_internal_token: "ChangeMeToSomethingRandom64CharactersLong11111111111111111111111"
```

## Dependencies

None.

## Example Playbook

```yaml
- hosts: forgejo_hosts
  roles:
    - your_github_username.ludus_forgejo
  vars:
    ludus_forgejo_domain: "forgejo.lab.local"
    ludus_forgejo_http_port: 3000
    ludus_forgejo_admin_password: "Sup3rS3cr3t!"
    ludus_forgejo_setup_nginx: true
```

## Example Ludus Range Config

```yaml
ludus:
  - vm_name: "{{ range_id }}-forgejo"
    hostname: "forgejo"
    template: debian-12-x64-server-template
    vlan: 10
    ip_last_octet: 50
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - your_github_username.ludus_forgejo
    role_vars:
      ludus_forgejo_version: "14.0.3"
      ludus_forgejo_domain: "10.{{ range_id | regex_replace('[^0-9]','') }}.10.50"
      ludus_forgejo_http_port: 3000
      ludus_forgejo_root_url: "http://10.{{ range_id | regex_replace('[^0-9]','') }}.10.50:3000/"
      ludus_forgejo_admin_user: "admin"
      ludus_forgejo_admin_password: "ForgejoPW1!"
      ludus_forgejo_secret_key: "ReplaceWithA64CharRandomString_________________________________1"
      ludus_forgejo_internal_token: "ReplaceWithA64CharRandomString_________________________________2"
      ludus_forgejo_create_admin: true
```

## Deploying

```bash
# 1. Add the role to your Ludus user
ludus ansible role add -d ./ludus_forgejo

# 2. Push range config
ludus range config set -f range-config.yml

# 3. Deploy only this role on your Forgejo VM
ludus range deploy -t user-defined-roles --limit YOUR_RANGE_ID-forgejo
```

After deployment, Forgejo is accessible at `http://<vm_ip>:3000`.

## License

BSD-2-Clause
