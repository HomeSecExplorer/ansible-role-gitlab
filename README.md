# Ansible Role: gitlab

[![CI](https://github.com/HomeSecExplorer/ansible-role-gitlab/actions/workflows/ci.yml/badge.svg)](https://github.com/HomeSecExplorer/ansible-role-gitlab/actions/workflows/ci.yml)
![Ansible Galaxy](https://img.shields.io/badge/ansible-galaxy-blue?logo=ansible)
![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)

---

**Author:** HomeSec Explorer  
**License:** MIT  
**Tags:** gitlab, ci, cd, scm, devops, selfhosted, homelab

## Description

This role installs and configures GitLab using the official Omnibus package.
It focuses on a clean, opinionated setup that is easy to automate and maintain in homelabs and small production environments.

The role:

- Installs the selected GitLab edition (Community or Enterprise, depending on your package source)
- Manages the main `gitlab.rb` using a Jinja2 template
- Optionally manages SSL certificates, including self signed certificates
- Optionally enables LDAP and SMTP integration
- Optionally enables the GitLab Container Registry
- Supports LetsEncrypt or another ACME provider for certificate management

---

## Requirements

- Ansible `>= 2.13`
- root or sudo privileges on managed hosts
- Access to the GitLab Omnibus package repository for your distribution

---

## Supported operating systems

- Debian 13 (Trixie)
- Ubuntu 24.04 (Noble)

> Note: the OS compatibility check (variable `hsegl_os_check`) validates platforms supported by this role. It does not reflect upstream GitLab support policies.

## Test matrix

**Legend:** :white_check_mark: manual test passed - :repeat: covered in CI - :white_circle: not tested

| Distro | Version | Manually verified | CI | Notes |
|:-------|:--------|:-----------------:|:--:|:-----|
| Debian | 13 | :white_check_mark: | :repeat: |  |
| Ubuntu | 24.04 | :white_check_mark: | :repeat: |  |

---

## Role variables (examples)

See `defaults/main.yml` for the full list.

```yaml
# Role control
hsegl_os_check: true                        # fail fast if the OS is not in this role's supported list
hsegl_config_template: "gitlab.rb.j2"       # template used to render /etc/gitlab/gitlab.rb

# Core GitLab configuration
hsegl_domain: "gitlab.example.com"          # FQDN that GitLab uses in URLs and links
hsegl_external_url: "https://{{ hsegl_domain }}/"
hsegl_edition: "gitlab-ce"                  # gitlab-ce or gitlab-ee, depending on repository
hsegl_version: ""                           # optional specific package version
hsegl_backup_path: "/var/opt/gitlab/backups"

# SSL and self signed certificates
hsegl_http_redirect: true                   # redirect HTTP to HTTPS using bundled NGINX

hsegl_create_self_signed_cert: true         # create a local self signed certificate if none exists

# LDAP authentication
hsegl_ldap_enabled: false

# SMTP email
hsegl_smtp_enable: false

# System email identity
hsegl_email_enabled: false

# GitLab Container Registry
hsegl_registry_enable: false

# LetsEncrypt and ACME
hsegl_letsencrypt_enable: false
hsegl_letsencrypt_staging_endpoint: ""
hsegl_letsencrypt_production_endpoint: ""

# Extra settings appended to gitlab.rb
hsegl_extra_settings:
  - puma:
      - key: "worker_processes"
        value: 4
  - sidekiq:
      - key: "concurrency"
        value: 18
  - prometheus_monitoring:
      - key: "enable"
        value: true
```

---

## Install this role

From Ansible Galaxy (recommended):

```bash
ansible-galaxy install HomeSecExplorer.gitlab
```

Or manually (via Git):

```bash
git clone https://github.com/HomeSecExplorer/ansible-role-gitlab.git roles/HomeSecExplorer.gitlab
```

---

## Example playbook

```yaml
- name: Install GitLab with HTTPS enabled
  hosts: gitlab_servers
  become: true
  vars:
    hsegl_domain: "gitlab.example.com"
    hsegl_external_url: "https://gitlab.example.com/"
    hsegl_create_self_signed_cert: true
  roles:
    - role: HomeSecExplorer.gitlab
```

Another minimal example with custom SMTP:

```yaml
- name: GitLab with LDAP and SMTP integration
  hosts: gitlab_servers
  become: true
  vars:
    hsegl_domain: "gitlab.example.com"
    # SMTP
    hsegl_smtp_enable: true
    hsegl_smtp_address: "smtp.example.com"
    hsegl_smtp_port: "587"
    hsegl_smtp_user_name: "gitlab@example.com"
    hsegl_smtp_password: "change-me"
    hsegl_smtp_domain: "example.com"
  roles:
    - role: HomeSecExplorer.gitlab
```

---

## Notes and tips

- Always test on a non production host first, especially when changing SSL, LDAP, or SMTP settings.
- After configuration changes, this role will trigger `gitlab-ctl reconfigure` when needed.
- For internet facing instances you should use valid certificates from LetsEncrypt or another CA instead of long lived self signed certificates.
- Regularly verify backups and set `hsegl_backup_keep_time` according to your retention policy.
- If you do not need the Container Registry, keep `hsegl_registry_enable: false` to simplify the setup.

---

## Acknowledgements

Inspired by:

- [geerlingguy/ansible-role-gitlab](https://github.com/geerlingguy/ansible-role-gitlab/tree/master)

---

## License

MIT

## Author Information

HomeSec Explorer  
🔗 [YouTube Channel](https://www.youtube.com/@HomeSecExplorer)

If this role was helpful, drop a ⭐ on GitHub, subscribe on YouTube or Sponsor me!
