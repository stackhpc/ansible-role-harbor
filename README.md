# Ansible Role: Harbor

An Ansible Role that installs [Harbor](https://github.com/goharbor/harbor) on Linux.

This role is in alpha stage. Issues and PRs are welcome.

## Requirements

None.

## Role Variables

For a complete list see `defaults/main.yaml`.

By default, the role uses the IP of the current host to set `harbor_hostname`. You can override it.

To change the install dir:

```yaml
harbor_install_dir: /opt
```

To change default protocol:

```yaml
harbor_url_protocol: "http"
```

If you want to change the exposed ports of Harbor's NGINX from the defaults of 80 and 443, use:

```yaml
harbor_exposed_http_port: 81
harbor_exposed_https_port: 444
```

Additional config options for `harbor.yml` can be overridden as follows (see https://github.com/goharbor/harbor/blob/master/make/harbor.yml.tmpl for additional configurations):

```yaml
harbor_extra_config: {}
```

To install with extras set:

```yaml
harbor_extras:
  - trivy
  - clair
  - notary
  - chartmuseum
```

You can also pass extra arguments to the installer with `harbor_installer_extra_args` (a string).

You may define `harbor_projects` if you want projects to be automatically created once harbor is installed.

```yaml
harbor_projects:
  - project_name: test
    is_public: "true"
    content_trust: "false"
    prevent_vul: "false"
    severity: "high"
    auto_scan: "true"
```


By default, users can self-register. If you prefer to create users automatically, you _must_ disable self-registration and set a list of users. Those users will be created automatically. The password defaults to "HarborUser12345".

This operation is idempotent.

```yaml
harbor_users:
  - username: dev
    email: dev@test.com
    sysadmin_flag: false
```

## Example Playbook

```yaml
---
- name: Installing and configuring Harbor
  hosts: harbor
  vars:
    harbor_projects:
      - project_name: test
        is_public: "true"
        content_trust: "false"
        prevent_vul: "false"
        severity: "high"
        auto_scan: "true"
    harbor_users:
      - username: dev
        email: dev@test.com
        sysadmin_flag: false
  tasks:
    - include_role: name=stackhpc.harbor
```

After the playbook runs, you should be able to navigate to your host on port 80/443 and see Harbor's UI. You can login with `admin/Harbor12345`. If you changed the exposed ports, remember to use them instead of 80/443.

## Author Information

This role was originally created in 2019 by [Nicholas Amorim](https://github.com/nicholasamorim).
It was simplified along with Harbor v2 support by [brtknr](https://github.com/brtknr)@[stackhpc](https://github.com/stackhpc).
