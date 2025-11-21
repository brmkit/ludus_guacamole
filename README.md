# ludus_guacamole

An Ansible Role that installs [Guacamole](https://guacamole.apache.org/) on Linux in a Ludus range, and optionally configures connections.

## Requirements

```bash
ludus ansible collection add scicore.guacamole
```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
    guacamole_install_dir: "/opt/guacamole"
    # check https://github.com/flcontainers/guacamole
    guacamole_extensions_list: auth-jdbc-postgresql
    guacamole_connections: []
      - name: "ConnectionName"
        hostname: "server-1"
        port: 3389
        protocol: rdp
        username: "rdpuser"
        password: "rdppass"
        domain: "DOMAIN"
      - name: "ConnectionName"
        hostname: "server-2"
        port: 22
        protocol: ssh
        username: "sshuser"
        password: "sshpass"
```
## Dependencies

Docker: [geerlingguy.docker](https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/docker/)

Guacamole Connections: [scicore.guacamole](https://galaxy.ansible.com/ui/repo/published/scicore/guacamole/)

## Example Ludus Range Config

```yaml
ludus:
  - vm_name: "{{ range_id }}-DC"
    hostname: "{{ range_id }}-DC"
    template: win2022-server-x64-us-template
    vlan: 10
    ip_last_octet: 11
    ram_gb: 8
    cpus: 4
    windows:
      sysprep: false
    domain:
      fqdn: guacamole.test
      role: primary-dc
  - vm_name: "{{ range_id }}-WIN10"
    hostname: "{{ range_id }}-WIN10"
    template: win10-22h2-x64-enterprise-template
    vlan: 10
    ip_last_octet: 21
    ram_gb: 8
    cpus: 4
    windows:
      sysprep: false
    domain:
      fqdn: guacamole.test
      role: member
  - vm_name: "{{ range_id }}-GUACAMOLE"
    hostname: "{{ range_id }}-GUACAMOLE"
    template: ubuntu-24.04-x64-server-template
    vlan: 10
    ip_last_octet: 50
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - ludus_guacamole
    role_vars:
      guacamole_connections:
        - name: "{{ range_id }}-DC"
          protocol: rdp
          domain: "GUACAMOLE"
          username: domainadmin
          password: password
          port: 3389
        - name: "Name Example"
          hostname: "{{ range_id }}-WIN10"
          protocol: rdp
          username: localuser
          password: password
          port: 3389
```

## License

[//]: # (If you change the License type, be sure to change the actual LICENSE file as well)
MIT

## Author Information

This role was created by [brmkit](https://github.com/brmkit), for [Ludus](https://ludus.cloud/).