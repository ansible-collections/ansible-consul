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

## v1.2.3

- Still with the tests

## v1.2.4

- Updated README
- Undo 125bd4bb369bb85f58a09b5dc81839e2779bd29f as dots in node_name breaks
  DNS API (without recursor option) and also breaks dnsmasq option

## v1.2.5

- Add LICENSE.txt for Apache 2.0 license

## v1.2.6

- Update documentation
- Add `consul_node_name` variable
- Add `consul_dns_bind_address` variable
- Add `consul_http_bind_address` variable
- Add `consul_https_bind_address` variable
- Add initial ACL variables

## v1.2.7

- Secondary nodes now join only the bootstrap node
- Added consul_bootstrap_interface variable
- Add PIDFile to systemd unit
- Updated documentation

## v1.2.8

- Stop creating UI directory
- Set correct RAM in Vagrantfile

## v1.2.9

- Download once, copy many for Consul binary
- Rename package variables

## v1.2.10

- Added consul_rpc_bind_address
- Updated documentation

## v1.2.11

- Update supported versions
- Fix up unarchive task quoting

## v1.2.12

- Update documentation

## v1.2.13

- Doc meta

## v1.2.14

- Documentation updates

## v1.2.15

- Fail on older versions
- Move distro vars to defaults
- Remove vars

## v1.2.16

- Clean up variables (thanks @jessedefer)
- Update documentation (thanks @jessedefer)
- Update CONTRIBUTORS

## v1.3.0

- Initial ACL support
- Initial Atlas support
- Streamline main tasks
- Update documentation
- Update variables

## v1.3.1

- Correct variable names
- Add token display variables
- Update documentation
- Remove deprecated variables

# v1.3.2

- Correct CONSUL_DNSMASQ_ENABLE var name

## v1.3.3

- Update/validate CentOS 7 box
- Update documentation
- Updated failure cases for CentOS

## v1.3.4

- Update documentation

## v1.4.0

- Compatibility with Ubuntu 16.04 (thanks @crumohr)
- iptables support (thanks @crumohr)
- Booleans instead of strings for variables (thanks @crumohr)
- Runnable if DNS is broken (thanks @crumohr)
- Remove unused variables
- Update block conditional for ACLs
- Update documentation

## v1.4.1

- Move Dnsmasq restart to inside of tasks
- Add client dependencies for further configuration (thanks @crumohr)
- Fix error using predefined encryption key (thanks @crumohr)
- Removal of redundant includes (thanks @crumohr)

## v1.5.0

- Add initial TLS support
- Update documentation

## v1.5.1

- Fail when ethernet interface specified by consul_iface not found on
  the system (addresses #13)

## v1.5.2

- Consul version 0.7.1
- Consistent template names
- Update documentation

## v1.5.3

- Update start_join for client configuration template

## v1.5.4

- Recursors as env var

## v1.5.5

- Initial attempts at idempotency in main tasks (for #14, #15)

## v1.5.6

- Correct Atlas variable names

## v1.5.7

- Remove unnecessary code (thanks @kostyrevaa)
- Determine binary's SHA 256 from releases.hashicorp.com (for #16)
- Update documentation

## v1.6.0

- Update task logic around initscripts (for #19)
- Fix issues in initscripts
- Rename Debian init script template
- Update documentation
- Fixing bug with deleting file. Better regex. Formatting. (Thanks @violuke)
- Remember ACL master/replication tokens between runs. 
  Actually set replication token. (Thanks @violuke)
- Typo fix (Thanks @violuke)
- Allowing re-running to add new nodes. More HA too. (Thanks @violuke)

## v1.6.1

- Drop Trusty support from meta for now (for #19)

## v1.6.2

- Stop reconfiguring bootstrap node as it's not really necessary and
  spurious races cause failure to re-establish cluster quorum when doing so
- CONSUL_VERSION environment variable
- Deprecated default variables cleanup

## v1.6.3

- Ensure that all local_action tasks have become: no (thanks @itewk)

## v1.7.0

- Consul version 0.7.2

## v1.7.2

- Fix non-working cleanup task
- Update README

## v1.7.3

- Version updates
- Task edits
- add CONTRIBUTING.md

## v1.7.4

- Consul 0.7.3
- Update documentation

## v1.8.0

- Consul 0.7.5
- BREAKING CHANGE: Deprecate read/write of ACL tokens from file system
  functionality and prefer setting tokens from existing cluster nodes with
  CONSUL_ACL_MASTER_TOKEN and  CONSUL_ACL_REPLICATION_TOKEN environment
  variables instead
- Update documentation

## v1.8.1

- Consul 0.7.5
- Update documentation
- Contributors correction
