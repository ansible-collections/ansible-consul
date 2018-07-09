# Consul

[![Build Status](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-consul)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.consul-blue.svg)](https://galaxy.ansible.com/brianshumate/consul/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Percentage of issues still open")

This Ansible role installs [Consul](https://consul.io/), including establishing a filesystem structure and server or client agent configuration with support for some common operational features.

It can bootstrap a development or evaluation cluster of 3 server agents running in a Vagrant and VirtualBox based environment. See [README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-consul/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

> “Another flaw in the human character is that everybody wants to build and nobody wants to do maintenance.”<br>
> ― Kurt Vonnegut, Hocus Pocus

Please note that this role is more concerned with the initial installation and bootstrapping of a running cluster environment and does not currently concern itself (all that much) with performing ongoing drif^H^H^H^H *maintenance* of an existing cluster.

Many users have expressed that the Vagrant based environment makes getting a
working local Consul server cluster environment up and running an easy process — so this role will target that experience as a primary motivator for existing.

If you get some mileage from it in other ways, then all the better!

## Requirements

This role requires a FreeBSD, Debian, or RHEL based Linux distribution or
Windows Server 2012 R2. It might work with other software versions, but is
definitely known to work with the following specific software versions:

* Consul: 1.2.0
* Ansible: 2.6.1
* CentOS: 7
* Debian: 9
* FreeBSD: 11
* RHEL: 7
* Ubuntu: 16.04
* Windows: Server 2012 R2

## Role Variables

The role uses variables defined in these three sources:

- `defaults/main.yml`
- `vars/*.yml`
- Hosts inventory file (see `examples/vagrant_hosts` for an example)

NOTE: The label for servers in the hosts inventory file must be
`[consul_instances]` as shown in the example. The role will not function
properly if the label name is anything else.

Many of these can also be further overridden by environment variables as well;
the variables are named and described below:

### `consul_version`

- Version to install
- Default value: *1.2.0*

### `consul_architecture_map`

- Dictionary for translating ansible_architecture to HashiCorp architecture
  naming convention
- Default value: dict

### `consul_architecture`

- System architecture as determined by `{{ consul_architecture_map[ansible_architecture] }}`
- Default value: *amd64*, *arm*, or *arm64* (determined at runtime)

### `consul_os`

- Node operating system name in lowercase representation
- Default value: `{{ ansible_os_family | lower }}`

### `consul_install_dependencies`

- Install python and package dependencies required for the role functions.
- Default value: yes

### `consul_zip_url`

- Consul archive download URL
- Default value: `https://releases.hashicorp.com/consul/{{ consul_version }}/consul_{{ consul_version }}_{{ consul_os }}_{{ consul_architecture }}.zip`

### `consul_checksum_file_url`

- Package SHA256 summaries URL
- Default value: `https://releases.hashicorp.com/consul/{{ consul_version }}/{{ consul_version }}_SHA256SUMS`

### `consul_bin_path`

- Binary installation path
- Default Linux value: `/usr/local/bin`
- Default Windows value: `C:\ProgramData\consul\bin`

### `consul_config_path`

- Base configuration file path
- Default Linux value: `/etc/consul`
- Default Windows value: `C:\ProgramData\consul\config`

### `consul_configd_path`

- Additional configuration directory
- Default Linux value: `{{ consul_config_path }}/consul.d`
- Default Windows value: `C:\ProgramData\consul\config.d`

### `consul_data_path`

- Data path as defined in [data_dir or -data-dir](https://www.consul.io/docs/agent/options.html#_data_dir)
- Default Linux value: `/var/consul`
- Default Windows value: `C:\ProgramData\consul\data`

### `consul_log_path`

- Log path for use in rsyslogd configuration on Linux.
- Default Linux value: `/var/log/consul`
  - Override with `CONSUL_LOG_PATH` environment variable
- Default Windows value: `C:\ProgramData\consul\log`

### `consul_log_file`

- Log file for use in rsyslogd configuration on Linux.
  - Override with `CONSUL_LOG_FILE` environment variable
- Default Linux value: `consul.log`

### `consul_syslog_facility`

- Syslog facility as defined in [syslog_facility](https://www.consul.io/docs/agent/options.html#syslog_facility)
  - Override with `CONSUL_SYSLOG_FACILITY` environment variable
- Default Linux value: *local0*

### `syslog_user`

- Owner of `rsyslogd` process on Linux. `consul_log_path`'s ownership is set to this user on Linux.
  - Override with `SYSLOG_USER` environment variable
- Default Linux value: *syslog*


### `syslog_group`

- Group of user running `rsyslogd` process on Linux. `consul_log_path`'s group ownership is set to this group on Linux.
  - Override with `SYSLOG_GROUP` environment variable
- Default value: *adm*

### `consul_run_path`

- Run path for PID file
- Default Linux value: `/var/run/consul`
- Default Windows value: `C:\ProgramData\consul`

### `consul_user`

- OS user
- Default Linux value: *consul*
- Default Windows value: *LocalSystem*

### `consul_group`

- OS group
- Default value: *bin*

### `consul_group_name`

- Inventory group name
  - Override with `CONSUL_GROUP_NAME` environment variable
- Default value: *consul_instances*

### `consul_retry_interval`

- Interval for reconnection attempts to LAN servers
- Default value: *30s*

### `consul_retry_interval_wan`

- Interval for reconnection attempts to WAN servers
- Default value: *30s*

### `consul_retry_max`

- Max reconnection attempts to LAN servers before failing, 0=infinit
- Default value: *0*

### `consul_retry_max_wan`

- Max reconnection attempts to WAN servers before failing, 0=infinit
- Default value: *0*

### `consul_join`

- List of LAN servers, not managed by this role, to join (ipv4 ipv6 or dns addresses)
- Default value: *[]*

### `consul_join_wan`

- List of WAN servers, not managed by this role, to join (ipv4 ipv6 or dns addresses)
- Default value: *[]*

### `consul_servers`

It's typically not necessary to manually alter this list.

- List of server nodes
- Default value: List of all nodes in `consul_group_name` with
  `consul_node_role` set to *server* or *bootstrap*

### `consul_gather_server_facts`

This feature makes it possible to gather the `consul_advertise_address(_wan)` from
servers that are currently not targeted by the playbook.

To make this possible the `delegate_facts` option is used; note that his
option has been problematic.

- Gather facts from servers that are not currently targeted
- Default value: 'no'

### `consul_datacenter`

- Datacenter label
  - Override with `CONSUL_DATACENTER` environment variable- Default value: *dc1*
- Default value: *dc1*

### `consul_domain`

- Consul domain name as defined in [domain or -domain](https://www.consul.io/docs/agent/options.html#_domain)
  - Override with `CONSUL_DOMAIN` environment variable
- Default value: *consul*

### `consul_node_meta`

- Consul node meta data (key-value)
- Supported in Consul version 0.7.3 or later
- Default value: *{}*
- Example:
```yaml
consul_node_meta:
    node_type: "my-custom-type"
    node_meta1: "metadata1"
    node_meta2: "metadata2"
```

### `consul_log_level`

- Log level as defined in [log_level or -log-level](https://www.consul.io/docs/agent/options.html#_log_level)
  - Override with `CONSUL_LOG_LEVEL` environment variable
- Default value: *INFO*

### `consul_syslog_enable`

- Log to syslog as defined in [enable_syslog or -syslog](https://www.consul.io/docs/agent/options.html#_syslog)
  - Override with `CONSUL_SYSLOG_ENABLE` environment variable
- Default Linux value: *true*
- Default Windows value: *false*

### `consul_iface`

- Consul network interface
  - Override with `CONSUL_IFACE` environment variable
- Default value: `{{ ansible_default_ipv4.interface }}`

### `consul_bind_address`

- Bind address
  - Override with `CONSUL_BIND_ADDRESS` environment variable
- Default value: default ipv4 address, or address of interface configured by
  `consul_iface`

### `consul_advertise_address`

- Lan advertise address
- Default value: `consul_bind_address`

### `consul_advertise_address_wan`

- Wan advertise address
- Default value: `consul_bind_address`

### `consul_advertise_addresses`

- Advanced advertise addresses settings
- Individual addresses kan be overwritten using the `consul_advertise_addresses_*` variables
- Default value:
  ```yaml
  consul_advertise_addresses:
    serf_lan: "{{ consul_advertise_addresses_serf_lan | default(consul_advertise_address+':'+consul_ports.serf_lan) }}"
    serf_wan: "{{ consul_advertise_addresses_serf_wan | default(consul_advertise_address_wan+':'+consul_ports.serf_wan) }}"
    rpc: "{{ consul_advertise_addresses_rpc | default(consul_bind_address+':'+consul_ports.server) }}"
  ```
### `consul_client_address`

- Client address
- Default value: *127.0.0.1*

### `consul_addresses`

- Advanced address settings
- Individual addresses kan be overwritten using the `consul_addresses_*` variables
- Default value:
  ```yaml
  consul_addresses:
    dns: "{{ consul_addresses_dns | default(consul_client_address, true) }}"
    http: "{{ consul_addresses_http | default(consul_client_address, true) }}"
    https: "{{ consul_addresses_https | default(consul_client_address, true) }}"
    rpc: "{{ consul_addresses_rpc | default(consul_client_address, true) }}"
  ```

### `consul_ports`

- The official documentation on the [Ports Used](https://www.consul.io/docs/agent/options.html#ports)
- The ports mapping is a nested dict object that allows setting the bind ports for the following keys:
  - dns - The DNS server, -1 to disable. Default 8600.
  - http - The HTTP API, -1 to disable. Default 8500.
  - https - The HTTPS API, -1 to disable. Default -1 (disabled).
  - rpc - The CLI RPC endpoint. Default 8400. This is deprecated in Consul 0.8 and later.
  - serf_lan - The Serf LAN port. Default 8301.
  - serf_wan - The Serf WAN port. Default 8302.
  - server - Server RPC address. Default 8300.

For example, to enable the consul HTTPS API it is possible to set the variable as follows:

- Default values:
```yaml
  consul_ports:
    dns: "{{ consul_ports_dns | default('8600', true) }}"
    http: "{{ consul_ports_http | default('8500', true) }}"
    https: "{{ consul_ports_https | default('-1', true) }}"
    rpc: "{{ consul_ports_rpc | default('8400', true) }}"
    serf_lan: "{{ consul_ports_serf_lan | default('8301', true) }}"
    serf_wan: "{{ consul_ports_serf_wan | default('8302', true) }}"
    server: "{{ consul_ports_server | default('8300', true) }}"
```

Notice that the dict object has to use precisely the names stated in the
documentation! And all ports must be specified. Overwriting one or multiple
ports can be done using the `consul_ports_*` variables.

### `consul_node_name`

- Node name (should not include dots)
- Default value: `{{ inventory_hostname_short }}`

### `consul_recursors`

- List of upstream DNS servers
  See [recursors](https://www.consul.io/docs/agent/options.html#recursors)
  - Override with `CONSUL_RECURSORS` environment variable
- Default value: Empty list

### `consul_bind_address`

- The interface address to bind to
- Default value: dynamic from hosts inventory

### `consul_dnsmasq_enable`

- Whether to install and configure DNS API forwarding on port 53 using DNSMasq
  - Override with `CONSUL_DNSMASQ_ENABLE` environment variable
- Default value: *false*

### `consul_iptables_enable`

- Whether to enable iptables rules for DNS forwarding to Consul
  - Override with `CONSUL_IPTABLES_ENABLE` environment variable
- Default value: *false*

### `consul_acl_policy`

- Add basic ACL config file
  - Override with `CONSUL_ACL_POLICY` environment variable
- Default value: *false*

### `consul_acl_enable`

- Enable ACLs
  - Override with `CONSUL_ACL_ENABLE` environment variable
- Default value: *false*

### `consul_acl_ttl`

- TTL for ACL's
  - Override with `CONSUL_ACL_TTL` environment variable
- Default value: *30s*

### `consul_acl_datacenter`

- ACL authoritative datacenter name
  - Override with `CONSUL_ACL_DATACENTER` environment variable
- Default value: *dc1*

### `consul_acl_down_policy`

- Default ACL down policy
  - Override with `CONSUL_ACL_DOWN_POLICY` environment variable
- Default value: *allow*

### `consul_acl_token`

- Default ACL token, only set if provided
  - Override with `CONSUL_ACL_TOKEN` environment variable
- Default value: /

### `consul_acl_agent_token`

- Used for clients and servers to perform internal operations to the service catalog. See: [acl_agent_token](https://www.consul.io/docs/agent/options.html#acl_agent_token)
  - Override with `CONSUL_ACL_AGENT_TOKEN` environment variable
- Default value: /

### `consul_acl_agent_master_token`

- A [special access token](https://www.consul.io/docs/agent/options.html#acl_agent_master_token) that has agent ACL policy write privileges on each agent where it is configured
  - Override with `CONSUL_ACL_AGENT_MASTER_TOKEN` environment variable
- Default value: /

### `consul_acl_default_policy`

- Default ACL policy
  - Override with `CONSUL_ACL_DEFAULT_POLICY` environment variable
- Default value: *allow*

### `consul_acl_master_token`

- ACL master token
  - Override with `CONSUL_ACL_MASTER_TOKEN` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_acl_master_token_display`

- Display generated ACL Master Token
  - Override with `CONSUL_ACL_MASTER_TOKEN_DISPLAY` environment variable
- Default value: *false*

### `consul_acl_replication_enable`

- Enable ACL replication without token (makes it possible to set the token
  trough the API)
  - Override with `CONSUL_ACL_REPLICATION_TOKEN_ENABLE` environment variable
- Default value: /

### `consul_acl_replication_token`

- ACL replication token
  - Override with `CONSUL_ACL_REPLICATION_TOKEN_DISPLAY` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_tls_enable`

- Enable TLS
  - Override with `CONSUL_ACL_TLS_ENABLE` environment variable
- Default value: *false*

### `consul_src_def`

- Default source directory for TLS files
  - Override with `CONSUL_ACL_TLS_ENABLE` environment variable
- Default value: `{{ role_path }}/files`

### `consul_tls_src_files`

- User-specified source directory for TLS files
  - Override with `CONSUL_TLS_SRC_FILES` environment variable
- Default value: `{{ role_path }}/files`

### `consul_tls_dir`

- Target directory for TLS files
  - Override with `CONSUL_TLS_DIR` environment variable
- Default value: `/etc/consul/ssl`

### `consul_tls_ca_crt`

- CA certificate filename
  - Override with `CONSUL_TLS_CA_CRT` environment variable
- Default value: `ca.crt`

### `consul_tls_server_crt`

- Server certificate
  - Override with `CONSUL_TLS_SERVER_CRT` environment variable
- Default value: `server.crt`

### `consul_server_key`

- Server key
  - Override with `CONSUL_TLS_SERVER_KEY` environment variable
- Default value: `server.key`

### `consul_tls_files_remote_src`

- Copy from remote source if TLS files are already on host
- Default value: 'no'

## `consul_encrypt_enable`

- Enable Gossip Encryption
- Default value: `true`

## `consul_raw_key`

- Set the encryption key; should be the same across a cluster. If not present the key will be generated & retrieved from the bootstrapped server.
- Default value: ``

### `consul_tls_verify_incoming`

- Verify incoming connections
  - Override with `CONSUL_TLS_VERIFY_INCOMING` environment variable
- Default value: *false*

### `consul_tls_verify_outgoing`

- Verify outgoing connections
  - Override with `CONSUL_TLS_VERIFY_OUTGOING` environment variable
- Default value: *true*

### `consul_tls_verify_server_hostname`

- Verify server hostname
  - Override with `CONSUL_TLS_VERIFY_SERVER_HOSTNAME` environment variable
- Default value: *false*

### `consul_install_remotely`

- Whether to download the files for installation directly on the remote hosts
- This is the only option on Windows as WinRM is somewhat limited in this scope
- Default value: *false*

### `consul_install_upgrade`

- Whether to [upgrade consul](https://www.consul.io/docs/upgrading.html) when a new version is specified
- The role does not handle the orchestration of a rolling update of servers followed by client nodes
- This option is not available for Windows, yet. (PR welcome)
- Default value: *false*

### `consul_ui`

- Enable the consul ui?
- Default value: *true*

### `consul_disable_update_check`

- Disable the consul update check?
- Default value: *false*

### `consul_enable_script_checks`

- Enable script based checks?
- Default value: *false*

### `consul_raft_protocol`

- Raft protocol to use.
- Default value:
- `consul_version` <= `0.7.0`: *1*
- `consul_version` > `0.7.0`: *3*

### `consul_node_role`

- The Consul role of the node, one of: *bootstrap*, *server*, or *client*
- Default value: *client*

One server should be designated as the bootstrap server, and the other
servers will connect to this server. You can also specify *client* as the
role, and Consul will be configured as a client agent instead of a server.

There are two methods to setup a cluster, the first one is to explicitly choose
the bootstrap server, the other one is to let the servers elect a leader among
themselves.

Here is an example of how the hosts inventory could be defined for a simple
cluster of 3 servers, the first one being the designated bootstrap / leader:

```yaml
[consul_instances]
consul1.consul consul_node_role=bootstrap
consul2.consul consul_node_role=server
consul3.consul consul_node_role=server
consul4.local consul_node_role=client
```

Or you can use the simpler method of letting them do their election process:

```yaml
[consul_instances]
consul1.consul consul_node_role=server consul_bootstrap_expect=true
consul2.consul consul_node_role=server consul_bootstrap_expect=true
consul3.consul consul_node_role=server consul_bootstrap_expect=true
consul4.local consul_node_role=client
```

> Note that this second form is the prefered one, because it is simpler.


### `consul_autopilot_enable`

Autopilot is a set of new features added in Consul 0.8 to allow for automatic operator-friendly management of Consul servers. It includes cleanup of dead servers, monitoring the state of the Raft cluster, and stable server introduction.

https://www.consul.io/docs/guides/autopilot.html

- Enable Autopilot config (will be written to bootsrapper node)
  - Override with `CONSUL_AUTOPILOT_ENABLE` environment variable
- Default value: *false*

#### `consul_autopilot_cleanup_dead_Servers`

Dead servers will periodically be cleaned up and removed from the Raft peer set, to prevent them from interfering with the quorum size and leader elections. This cleanup will also happen whenever a new server is successfully added to the cluster.

- Enable Autopilot config (will be written to bootsrapper node)
  - Override with `CONSUL_AUTOPILOT_CLEANUP_DEAD_SERVERS` environment variable
- Default value: *false*

#### `consul_autopilot_last_contact_threshold`

Used in the serf health check to determine node health.

- Sets the threshold for time since last contact
  - Override with `CONSUL_AUTOPILOT_LAST_CONTACT_THRESHOLD` environment variable
- Default value: '200ms'

#### `consul_autopilot_max_trailing_logs`

- Used in the serf health check to set a max-number of log entries nodes can trail the leader
  - Override with `CONSUL_AUTOPILOT_MAX_TRAILING_LOGS` environment variable
- Default value: 250


#### `consul_autopilot_server_stabilization_time`

- Time to allow a new node to stabilize
  - Override with `CONSUL_AUTOPILOT_SERVER_STABILIZATION_TIME` environment variable
- Default value: '10s'

#### `consul_autopilot_redundancy_zone_tag`

_Consul Enterprise Only (requires that CONSUL_ENTERPRISE is set to true)_

  - Override with `CONSUL_AUTOPILOT_REDUNDANCY_ZONE_TAG` environment variable
- Default value: 'az'

#### `consul_autopilot_disable_upgrade_migration`

_Consul Enterprise Only (requires that CONSUL_ENTERPRISE is set to true)_

  - Override with `CONSUL_AUTOPILOT_DISABLE_UPGRADE_MIGRATION` environment variable
- Default value: **false**

#### `consul_autopilot_upgrade_version_tag`

_Consul Enterprise Only (requires that CONSUL_ENTERPRISE is set to true)_

  - Override with `CONSUL_AUTOPILOT_UPGRADE_VERSION_TAG` environment variable
- Default value: ''



#### Custom Configuration Section

As Consul loads the configuration from files and directories in lexical order,
typically merging on top of previously parsed configuration files, you may set
custom configurations via `consul_config_custom`, which will be expanded into a file named `config_z_custom.json` within your `consul_config_path` which will
be loaded after all other configuration by default.

An example usage for enabling `telemetry`:

```yaml
  vars:
    consul_config_custom:
      telemetry:
        dogstatsd_addr: "localhost:8125"
        dogstatsd_tags:
          - "security"
          - "compliance"
        disable_hostname: true
```

## OS Distribution Variables

The `consul` binary works on most Linux platforms and is not distribution
specific. However, some distributions require installation of specific OS
packages with different package names.

### `consul_centos_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_centos_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_centos_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_centos_os_packages`

- List of OS packages to install
- Default value: list

### `consul_debian_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_debian_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_debian_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_debian_os_packages`

- List of OS packages to install
- Default value: list

### `consul_redhat_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_redhat_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_redhat_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_redhat_os_packages`

- List of OS packages to install
- Default value: list

### `consul_ubuntu_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_linux_amd64.zip`

### `consul_ubuntu_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_ubuntu_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_ubuntu_os_packages`

- List of OS packages to install
- Default value: list

### `consul_windows_pkg`

- Consul package filename
- Default value: `{{ consul_version }}_windows_amd64.zip`

### `consul_windows_url`

- Consul package download URL
- Default value: `{{ consul_zip_url }}`

### `consul_windows_sha256`

- Consul download SHA256 summary
- Default value: SHA256 SUM

### `consul_windows_os_packages`

- List of OS packages to install
- Default value: list

### `consul_performance`

- List of Consul performance tuning items
- Default value: list

#### `raft_multiplier`

- [Raft multiplier](https://www.consul.io/docs/agent/options.html#raft_multiplier) scales key Raft timing parameters
- Default value: 1

#### `leave_drain_time`

- [Node leave drain time](https://www.consul.io/docs/agent/options.html#leave_drain_time) is the dwell time for a server to honor requests while gracefully leaving

- Default value: 5s

#### `rpc_hold_timeout`

- [RPC hold timeout](https://www.consul.io/docs/agent/options.html#rpc_hold_timeout) is the duration that a client or server will retry internal RPC requests during leader elections
- Default value: 7s

## Dependencies

Ansible requires GNU tar and this role performs some local use of the
unarchive module, so ensure that your system has `gtar` installed and
in the PATH.

If you're on system with a different (i.e. BSD) `tar`, like macOS and you
see odd errors during unarchive tasks, you could be missing `gtar`.

Installing Ansible on Windows requires the PowerShell Community Extensions.
These already installed on Windows Server 2012 R2 and onward. If you're
attempting this role on Windows Server 2008 or earlier, you'll want to install
the extensions [here](https://pscx.codeplex.com/).

## Example Playbook

Basic installation is possible using the included `site.yml` playbook:

```
ansible-playbook -i hosts site.yml
```

You can also pass variables in using the `--extra-vars` option to the
`ansible-playbook` command:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_datacenter=maui"
```

Be aware that for clustering, the included `site.yml` does the following:

1. Executes consul role (installs Consul and bootstraps cluster)
2. Reconfigures bootstrap node to run without bootstrap-expect setting
3. Restarts bootstrap node

### ACL Support

Basic support for ACLs is included in the role. You can set the environment
variables `CONSUL_ACL_ENABLE` to *true*, and also set the
`CONSUL_ACL_DATACENTER` environment variable to its correct value for your
environment prior to executing your playbook; for example:

```
CONSUL_ACL_ENABLE=true CONSUL_ACL_DATACENTER=maui \
CONSUL_ACL_MASTER_TOKEN_DISPLAY=true ansible-playbook -i uat_hosts aloha.yml
```

If you want the automatically generated ACL Master Token value emitted to
standard out during the play, set the environment variable
`CONSUL_ACL_MASTER_TOKEN_DISPLAY` to *true* as in the above example.

If you want to use existing tokens, set the environment variables
`CONSUL_ACL_MASTER_TOKEN` and `CONSUL_ACL_REPLICATION_TOKEN` as well,
for example:

```
CONSUL_ACL_ENABLE=true CONSUL_ACL_DATACENTER=stjohn \
CONSUL_ACL_MASTER_TOKEN=0815C55B-3AD2-4C1B-BE9B-715CAAE3A4B2 \
CONSUL_ACL_REPLICATION_TOKEN=C609E56E-DD0B-4B99-A0AD-B079252354A0 \
CONSUL_ACL_MASTER_TOKEN_DISPLAY=true ansible-playbook -i uat_hosts sail.yml
```

There are a number of Ansible ACL variables you can override to further
refine your initial ACL setup. They are not all currently picked up from
environment variables, but do have some sensible defaults.

Check `defaults/main.yml` to see how some of he defaults (i.e. tokens)
are automatically generated.

### Dnsmasq DNS Forwarding Support

The role now includes support for [DNS forwarding](https://www.consul.io/docs/guides/forwarding.html) with [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html).

Enable like this:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_dnsmasq_enable=true"
```

Then, you can query any of the agents via DNS directly via port 53,
for example:

```
dig @consul1.consul consul3.node.consul

; <<>> DiG 9.8.3-P1 <<>> @consul1.consul consul3.node.consul
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29196
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;consul3.node.consul.   IN  A

;; ANSWER SECTION:
consul3.node.consul.  0 IN  A 10.1.42.230

;; Query time: 42 msec
;; SERVER: 10.1.42.210#53(10.1.42.210)
;; WHEN: Sun Aug  7 18:06:32 2016
;;
```

### `consul_dnsmasq_consul_address`

- Address used by dnsmasq to query consul
- Default value: `consul_address.dns`
- Defaults to 127.0.0.1 if consul's DNS is bound to all interfaces (eg `0.0.0.0`)

### `consul_dnsmasq_cache`

- dnsmasq cache-size
- If smaller then 0, the default dnsmasq setting will be used.
- Default value: *-1*

### `consul_dnsmasq_servers`

- Upstream DNS servers used by dnsmasq
- Default value: *8.8.8.8* and *8.8.4.4*

### `consul_dnsmasq_revservers`

- Reverse lookup subnets
- Default value: *[]*

### `consul_dnsmasq_no_poll`

- Do not poll /etc/resolv.conf
- Default value: false

### `consul_dnsmasq_no_resolv`

- Ignore /etc/resolv.conf file
- Default value: false

### `consul_dnsmasq_local_service`

- Only allow requests from local subnets
- Default value: false

### `consul_dnsmasq_listen_addresses`

- Custom list of addresses to listen on.
- Default value: *[]*


### iptables DNS Forwarding Support

This role can also use iptables instead of Dnsmasq for forwarding DNS queries
to Consul. You can enable it like this:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_iptables_enable=true"
```

> Note that iptables forwarding and Dnsmasq forwarding cannot be used
> simultaneously and the execution of the role will stop with error if such
> a configuration is specified.

### TLS Support

You can enable TLS encryption by dropping a CA certificate, server
certificate, and server key into the role's `files` directory.

By default these are named:

- `ca.crt` (can be overridden by {{ consul_tls_ca_crt }})
- `server.crt` (can be overridden by {{ consul_tls_server_crt }})
- `server.key` (can be overridden by {{ consul_server_key }})

Then either set the environment variable `CONSUL_TLS_ENABLE=true` or use the
Ansible variable `consul_tls_enable=true` at role runtime.

### Vagrant and VirtualBox

See [examples/README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) for details on quick Vagrant deployments
under VirtualBox for development, evaluation, testing, etc.

## License

BSD

## Author Information

[Brian Shumate](http://brianshumate.com)

## Contributors

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTORS.md) for their
contributions to this project.

Contributions are welcome, provided that you can agree to the terms outlined
in [CONTRIBUTING.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTING.md).
