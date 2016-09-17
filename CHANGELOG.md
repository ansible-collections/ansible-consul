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

## v1.0.10

- Remove extra datacenter definition

## v1.0.11

- Renamed bootstrap template

## v1.0.12

- FIX: No such file or directory /etc/init.d/functions (thanks @oliverprater)
- FIX: Using bare variables is deprecated (thanks @oliverprater)
- Added CONTRIBUTORS.md
- Updated documentation

## v1.0.13

- Add initial dnsmasq front end bits
- Reconfigure bootstrap node for normal operation (remove bootstrap-expect)
  after initial cluster formation and restart bootstrap node

## v1.0.14

- Initial test
- Initial Travis CI setup

## v1.0.15

- Meta update

## v1.1.0

- Bare role now installs and bootstraps cluster; included site.yml will also
  reconfigure bootstrap node as server and optionally enable dnsmasq
  forwarding for all cluster agents
- Remove bad client_addr bind in favor of default (localhost)
  Some weirdness was occurring whereby the client APIs were listening on
  TCP6/UDP6 sockets but not TCP4/UDP4 when client_addr set to 0.0.0.0
- Adjust timeouts for cluster UI check
- Default configurable domain to "consul" so that examples from docs work, etc.
- Combine all OS vars into main (addresses undefined var warnings)
- Removed separate OS var files
- Updated known working software versions
- Any errors are fatal for the site.yml example playbook
- Explicit pid-file to use in wait_for
- Remove cruft from init script
- Update documentation

## v1.2.0

- Consul version 0.7.0
- UI is built in now, so no longer downloaded / installed separately
- Usability improvements (thanks @Rodjers)

## v1.2.1

- Tests work locally but not in Travis; trying an env var instead of cfg

## v1.2.2

- Updated README
