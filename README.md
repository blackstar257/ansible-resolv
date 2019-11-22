# Ansible Role: resolv

[![Ansible Role](https://img.shields.io/badge/role-blackstar257.resolv-blue.svg)](https://galaxy.ansible.com/blackstar257/resolv/)
[![Build Status](https://travis-ci.com/blackstar257/ansible-resolv.svg?branch=master)](https://travis-ci.com/blackstar257/ansible-resolv)

Configures /etc/resolv.conf

## Requirements

This role requires Ansible 1.9 or higher.

## Role Variables

| Name                       | Default                  | Description                     |
| :------------------------- | :----------------------- | :------------------------------ |
| resolv_conf_nameservers    | ["1.1.1.1", "1.0.0.1"]   | List of nameserver IP addresses |
| resolv_conf_search_domains | ["{{ ansible_domain }}"] | List of search domains          |
| resolv_conf_options        | ["rotate"]               | List of search domains          |

## Dependencies

None

## Example Playbook

Configure /etc/resolv.conf file with default values

```yaml
- hosts: all
  roles:
    - blackstar257.resolv
```

Configure /etc/resolv.conf file specifying nameservers and a search domain

```yaml
- hosts: all
  vars:
    resolv_conf_nameservers:
      - 8.8.4.4
      - 8.8.8.8
    resolv_conf_search_domains:
      - google.com
  roles:
    - blackstar257.resolv
```

## License

BSD

## Author Information

Kevin Brebanov
Forked by Blackstar
