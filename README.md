# Ansible Role: Resolv

[![CI](https://github.com/blackstar257/ansible-resolv/workflows/CI/badge.svg)](https://github.com/blackstar257/ansible-resolv/actions?query=workflow%3ACI)

An Ansible role that configures `/etc/resolv.conf` for DNS resolution on Linux systems.

## Requirements

* Ansible 2.10 or higher
* Target systems must be Linux-based (RHEL/CentOS/Rocky/Alma/Ubuntu/Debian/Fedora)

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable              | Default                                    | Description                               |
| --------------------- | ------------------------------------------ | ----------------------------------------- |
| `resolv_nameservers`  | `["1.1.1.1", "1.0.0.1"]`                  | List of up to 3 DNS nameserver IP addresses |
| `resolv_domain`       | `''`                                       | Local domain name                         |
| `resolv_search`       | `["{{ ansible_domain \| default('') }}"]` | List of up to 6 domains to search for host-name lookup |
| `resolv_sortlist`     | `''`                                       | List of IP-address and netmask pairs to sort addresses returned by gethostbyname |
| `resolv_options`      | `["rotate"]`                              | List of options to modify certain internal resolver variables |

### Nameservers

The `resolv_nameservers` variable accepts a list of up to 3 IP addresses for DNS servers. Common options include:

* **Cloudflare**: `1.1.1.1`, `1.0.0.1`
* **Google**: `8.8.8.8`, `8.8.4.4`
* **Quad9**: `9.9.9.9`, `149.112.112.112`

### Domain and Search

* **`resolv_domain`**: Specifies the local domain name. This is used when a hostname is not fully qualified.
* **`resolv_search`**: List of up to 6 domains to search when resolving short hostnames. By default, it uses the system's domain if available.

### Sort List

The `resolv_sortlist` variable allows you to specify IP address and netmask pairs to sort addresses returned by `gethostbyname`. This can be useful for preferring local network addresses. It can be specified as:

* **String**: `192.168.1.0/255.255.255.0`
* **List**: `["192.168.1.0/255.255.255.0", "10.0.0.0/255.0.0.0"]`

### Resolver Options

Common resolver options include:

* **rotate**: Rotate through nameservers for load balancing
* **timeout:n**: Set timeout for queries (in seconds)
* **attempts:n**: Number of attempts per nameserver
* **ndots:n**: Number of dots required before trying search domains

## Dependencies

None

## Example Playbook

### Basic usage (Cloudflare DNS)

```yaml
- hosts: all
  become: true
  roles:
    - blackstar257.resolv
```

### Custom nameservers and search domain

```yaml
- hosts: all
  become: true
  vars:
    resolv_nameservers:
      - 1.1.1.1
      - 8.8.8.8
    resolv_domain: foo.org
    resolv_search:
      - foo.bar
      - foobar.com
    resolv_options:
      - timeout:2
      - rotate
  roles:
    - blackstar257.resolv
```

### Advanced configuration with sortlist

```yaml
- hosts: all
  become: true
  vars:
    resolv_nameservers:
      - 9.9.9.9
      - 149.112.112.112
    resolv_domain: internal.company.com
    resolv_search:
      - internal.company.com
      - company.com
    resolv_sortlist:
      - 192.168.1.0/255.255.255.0
      - 10.0.0.0/255.0.0.0
    resolv_options:
      - rotate
      - timeout:2
      - attempts:3
  roles:
    - blackstar257.resolv
```

### Enterprise environment

```yaml
- hosts: all
  become: true
  vars:
    resolv_nameservers:
      - 192.168.1.10    # Primary internal DNS
      - 192.168.1.11    # Secondary internal DNS
      - 8.8.8.8         # Fallback external DNS
    resolv_domain: corp.company.com
    resolv_search:
      - corp.company.com
      - company.com
    resolv_sortlist:
      - 192.168.0.0/255.255.0.0
    resolv_options:
      - rotate
      - timeout:1
      - attempts:2
      - ndots:2
  roles:
    - blackstar257.resolv
```

## Supported Platforms

* RHEL/CentOS 7, 8, 9
* Rocky Linux 8, 9
* AlmaLinux 8, 9
* Ubuntu 18.04, 20.04, 22.04, 24.04
* Debian 10, 11, 12
* Fedora 37+

## Testing

This role is tested using Molecule with Docker.

```bash
# Install dependencies
pip install molecule molecule-plugins[docker] docker

# Run tests
molecule test
```

## Ubuntu-Specific Behavior

On Ubuntu systems, this role performs additional steps:

1. **Removes symbolic links**: If `/etc/resolv.conf` is a symbolic link (common with `systemd-resolved`), it removes the link
2. **Prevents DHCP updates**: Installs a `dhclient` hook to prevent DHCP from overwriting `/etc/resolv.conf`

## Important Notes

* **Backup**: The role automatically creates backups of the original `/etc/resolv.conf`
* **Ubuntu systemd-resolved**: This role will disable automatic DNS management by `systemd-resolved`
* **Network Manager**: May conflict with NetworkManager's DNS management
* **DHCP clients**: The role prevents DHCP clients from updating DNS settings on Ubuntu
* **Limits**: The resolver enforces limits of 3 nameservers and 6 search domains per RFC standards

## License

MIT

## Author Information

This role was originally created by Kevin Brebanov and is now maintained by [blackstar257](https://github.com/blackstar257).

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests: `molecule test`
5. Submit a pull request
