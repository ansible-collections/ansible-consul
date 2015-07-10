## v1.0.0

- Installs Consul and Consul UI to each node
- Installs example configuration for bootstrap, server, and client
- Installs example upstart script

## v1.0.1

- Updated README

## v1.0.2

- Removed the need for consul_nodes variable
- Fix client template task
- Fix invalid JSON in the config.json outputs
- Updated documentation

## v1.0.3

- Add /usr/local/bin to PATH for cases where the consul binary is not found
- Changed UI path
- Updated Upstart script
- Added generic SysV init script
- Added Debian init script
- Use Upstart for distribution major versions < 7
- Use systemd for distribution major versions >= 7
