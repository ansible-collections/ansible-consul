## v1.0.0

- Installs Consul and Consul UI to each node
- Installs example configuration for bootstrap, server, and client
- Installs example upstart script

## v1.0.1

- Updated README

## v1.0.2

- Removed the need for cluster_nodes variable
- Fix client template task
- Fix invalid JSON in the config.json outputs
- Updated documentation

## v1.0.3

- Prefix /usr/local/bin in PATH for cases where the consul binary is not found
- Changed UI path
- Add generic SysV init script
- Add Debian init script
- Use systemd for distribution major versions >= 7
- Remove Upstart script
- Updated configuration files

## v1.0.4

- Renamed consul_nodes label for better compatibility with my other roles

## v1.0.5

- Updated defaults and Consul version (thanks @bscott)
- Made cluster bootable and switch to become_user + other Ansibel best
  practices (thanks @Rodjers)
- Updated minimum Ansible version required in meta

## v1.0.6

- Updated to Consul 0.6.4
- Make bind_address configurable for #1
- Cleaned up deprecaed bare variables
- Updated supporting software versions
- Updated documentation

## v1.0.7

- Update supported versions
- Update documentation

## v1.0.8

- Update documentation

## v1.0.9

- Change datacenter value
