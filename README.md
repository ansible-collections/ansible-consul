# Consul

[![Build Status](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-consul)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.consul-blue.svg)](https://galaxy.ansible.com/brianshumate/consul/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Percentage of issues still open")

This Ansible role installs [Consul](https://consul.io/), including establishing
a filesystem structure and server or client agent configuration with support
for some common operational features.

It can bootstrap a development or evaluation cluster of 3 server agents running
in a Vagrant and VirtualBox based environment. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-consul/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

> “Another flaw in the human character is that everybody wants to build and nobody wants to do maintenance.”<br>
> ― Kurt Vonnegut, Hocus Pocus

Please note that this role is more concerned with the initial installation and
bootstrapping of a running cluster environment and does not currently concern
itself (all that much) with performing ongoing drif^H^H^H^H *maintenance* of
an existing cluster.

Many users have expressed that the Vagrant based environment makes getting a
working local cluster environment up and running an easy process — so this
role will target that experience as a primary motivator for existing.

## Requirements

This role requires FreeBSD, or a Debian or RHEL based Linux distribution or
Windows Server 2012 R2. It might work with other software versions, but does
definitely work with the following specific software and versions:

* Consul: 0.8.5
* Ansible: 2.3.1.0
* CentOS: 7
* Debian: 8
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
`[cluster_nodes]` as shown in the example. The role will not function
properly if the label name is anything else.

Many of these can also be further overridden by environment variables as well;
the variables are named and described below:

### `consul_version`

- Version to install
- Default value: *0.8.5*

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
- Default Linux value: `/etc/consul.d`
- Default Windows value: `C:\ProgramData\consul\config.d`

### `consul_data_path`

- Data path as defined in [data_dir or -data-dir](https://www.consul.io/docs/agent/options.html#_data_dir)
- Default Linux value: `/var/consul`
- Default Windows value: `C:\ProgramData\consul\data`

### `consul_log_path`

- Log path (not implemented)
- Default Linux value: `/var/log/consul`
- Default Windows value: `C:\ProgramData\consul\log`

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
- Default value: *cluster_nodes*

### `consul_servers`

It's typically not necessary to manually alter this list.

- List of server nodes
- Default value: List of all nodes in `consul_group_name` with
  `consul_node_role` set to *server* or *bootstrap*

### `consul_gather_server_facts`

This feature makes it possible to gather the `consul_bind_address` from
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
- Default value: *127.0.0.1*

### `consul_dns_bind_address`

- DNS API bind address
- Default value: *127.0.0.1*

### `consul_http_bind_address`

- HTTP API bind address
- Default value: *0.0.0.0*

### `consul_https_bind_address`

- HTTPS API bind address
- Default value: *0.0.0.0*

### `consul_rpc_bind_address`

- RPC bind address
- Default value: *0.0.0.0*

### `consul_ports`

- Port Mappings
- Default value:
  - `rpc`: `8400`
  - `http`: `8500`
  - `https`: `-1`
  - `dns`: `8600`

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

### `consul_acl_enable`

- Enable ACLs
  - Override with `CONSUL_ACL_ENABLE` environment variable
- Default value: *false*

### `consul_acl_datacenter`

- ACL authoritative datacenter name
  - Override with `CONSUL_ACL_DATACENTER` environment variable
- Default value: *dc1*

### `consul_acl_default_policy`

- Default ACL policy
  - Override with `CONSUL_ACL_DEFAULT_POLICY` environment variable
- Default value: *allow*

### `consul_acl_down_policy`

- Default ACL down policy
  - Override with `CONSUL_ACL_DOWN_POLICY` environment variable
- Default value: *allow*

### `consul_acl_master_token`

- ACL master token
  - Override with `CONSUL_ACL_MASTER_TOKEN` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_acl_master_token_display`

- Display generated ACL Master Token
  - Override with `CONSUL_ACL_MASTER_TOKEN_DISPLAY` environment variable
- Default value: *false*

### `consul_acl_replication_token`

- ACL replication token
  - Override with `CONSUL_ACL_REPLICATION_TOKEN_DISPLAY` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_acl_agent_token`

- Used for clients and servers to perform internal operations to the service catalog. See: [acl_agent_token](https://www.consul.io/docs/agent/options.html#acl_agent_token)
  - Override with `CONSUL_ACL_AGENT_TOKEN` environment variable
- Default value: *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL*

### `consul_acl_agent_master_token`

- A [special access token](https://www.consul.io/docs/agent/options.html#acl_agent_master_token) that has agent ACL policy write privileges on each agent where it is configured
  - Override with `CONSUL_ACL_AGENT_MASTER_TOKEN` environment variable
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

### `consul_ui`

- Enable the consul ui?
- Default value: *true*

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
[cluster_nodes]
consul1.local consul_node_role=bootstrap
consul2.local consul_node_role=server
consul3.local consul_node_role=server
consul4.local consul_node_role=client
```

Or you can use the simpler method of letting them do their election process:

```yaml
[cluster_nodes]
consul1.local consul_node_role=server consul_bootstrap_expect=true
consul2.local consul_node_role=server consul_bootstrap_expect=true
consul3.local consul_node_role=server consul_bootstrap_expect=true
consul4.local consul_node_role=client
```

> Note that this second form is the prefered one, because it is simpler.

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
dig @consul1.local consul3.node.consul

; <<>> DiG 9.8.3-P1 <<>> @consul1.local consul3.node.consul
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

### `consul_dnsmasq_cache`

- dnsmasq cache-size
- If smaller then 0, the default dnsmasq setting will be used.
- Default value: *-1*

### `consul_dnsmasq_servers`

- Upstream DNS servers used by dnsmasq
- Default value: *8.8.8.8* and *8.8.4.4*

### `consul_dnsmasq_revservers`

- Reverse lookup subnets
- Default value: *{}*

### `consul_dnsmasq_no_poll`

- Do not poll /etc/resolv.conf
- Default value: false

### `consul_dnsmasq_no_resolv`

- Ignore /etc/resolv.conf file
- Default value: false

### `consul_dnsmasq_local_service`

- Only allow requests from local subnets
- Default value: false

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

See `examples/README_VAGRANT.md` for details on quick Vagrant deployments
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
