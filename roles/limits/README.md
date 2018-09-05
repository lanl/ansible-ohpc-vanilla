limits
=============

Ansible role which manage system limits.

#### Variables

```yaml
limits_path: '/etc/security/limits.conf'

limits_limits:
  - "*    -    nofile    65535"

```

#### Usage

Add `limits` to your roles and set vars in your playbook file.

Example:

```yaml

- hosts: all

  roles:
    - limits

  vars:
    limits_path: '/etc/security/limits.conf'
    limits_limits:
    - *    -    nofile    200000
```

