# Ansible Role: frr exporter

> This role is fork from [ansible node exporter v0.19.0](https://github.com/cloudalchemy/ansible-node-exporter) role and works with similar principles.

## Description

Deploy prometheus [keepalived exporter](https://github.com/gen2brain/keepalived_exporter) using ansible.

## Requirements

- Ansible >= 2.7 (It might work on previous versions, but we cannot guarantee it)
- gnu-tar on Mac deployer host (`brew install gnu-tar`)

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name                                     | Default Value    | Description                                                                                                                                                                                                                                |
| ---------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `keepalived_exporter_version`            | 0.3.0            | Keepalived exporter package version. Also accepts latest as parameter.                                                                                                                                                                     |
| `keepalived_exporter_binary_local_dir`   | "/usr/local/bin" | Allows to use local packages instead of ones distributed on github. As parameter it takes a directory where `keepalived_exporter` binary is stored on host on which ansible is ran. This overrides `keepalived_exporter_version` parameter |
| `keepalived_exporter_web_listen_address` | "0.0.0.0:9650"   | Address on which frr exporter will listen                                                                                                                                                                                                  |
| `keepalived_exporter_system_group`       | "keepalived-exp" | Group which will be used in systemd config to start frr_exporter                                                                                                                                                                           |
| `keepalived_exporter_system_user`        | "keepalived-exp" | User which will be used in systemd config to start frr_exporter                                                                                                                                                                            |
| `keepalived_exporter_create_usergroup`   | true             | Create user and group im case true                                                                                                                                                                                                         |

## Example

### Playbook for Debian

Use it in a playbook as follows:

```yaml
- hosts: all
  roles:
    - satandyh.keepalived-exporter
```

### Playbook for VyOS

You should reuse frr user/group. For do that use in your group_vars `keepalived_exporter_create_usergroup: false`

```yaml
# Prometheus exporters: ... keepalived, ...
- name: Configure exporters
  hosts: all
  become: true
  gather_facts: false
  roles:
    # Frr exporter
    # extra vars need only for VyOS - because we not gather any facts
    # be careful with selinux installation - it will try to install selinux by default
    - role: keepalived-exporter
      vars:
        ansible_service_mgr: "systemd"
        ansible_architecture: "x86_64"
        ansible_distribution: "Debian"
        ansible_selinux:
          status: false
        ansible_mounts:
          - { mount: "/" }
      tags:
        - keepalived-exporter
```

## Limitations

Pay Your attention despite that I add a lot of OS Platforms in reality I do tests only at Debian and VyOS system only. But I'm sure that this role will also work for other too.

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.
