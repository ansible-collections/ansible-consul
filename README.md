# Consul

[![Build Status](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-consul)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.consul-blue.svg)](https://galaxy.ansible.com/brianshumate/consul/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-consul.svg)](http://isitmaintained.com/project/brianshumate/ansible-consul "Percentage of issues still open")

This Ansible role installs [Consul](https://consul.io/), including filesystem
structure and server or client configuration.

It can also bootstrap a development or evaluation cluster of 3 server
agents running in a Vagrant and VirtualBox based environment. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-consul/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

## Requirements

This role requires a Debian or RHEL based Linux distribution. It might work
with other software versions, but does work with the following specific
software and versions:

* Ansible: 2.2.1.0
* Consul: 0.7.4
* Debian: 8
* CentOS: 7

## Role Variables

The role defines variables in `defaults/main.yml` and in the hosts
inventory file (see below):

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `consul_version` | *0.7.4* | Version to install |
| `consul_zip_url` | `https://releases.hashicorp.com/consul/{{ consul_version }}/consul_{{ consul_version }}_linux_amd64.zip` | Download URL |
| `consul_checksum_file_url` | "https://releases.hashicorp.com/consul/{{ consul_version }}/{{ consul_version }}_SHA256SUMS" | URL to package SHA256 summaries |
| `consul_bin_path` | `/usr/local/bin` | Binary installation path |
| `consul_config_path` | `/etc/consul.d` | Configuration file path |
| `consul_data_path` | `/var/consul` | Data path |
| `consul_log_path` | `/var/log/consul` | Log path |
| `consul_user` | `consul` | OS user |
| `consul_group` | `bin` | OS group |
| `consul_group_name` | *cluster_nodes* | Inventory group name |
| `consul_datacenter` | *dc1* | Datacenter label |
| `consul_domain` | `consul` | Consul domain name |
| `consul_log_level` | `INFO` | Log level |
| `consul_syslog_enable` | *true* | Log to syslog |
| `consul_iface` | `eth1` | Consul network interface |
| `consul_bind_address` | *127.0.0.1* | Bind address |
| `consul_dns_bind_address` | *127.0.0.1* | DNS API bind address |
| `consul_http_bind_address` | *0.0.0.0* | HTTP API bind address |
| `consul_https_bind_address` | *0.0.0.0* | HTTPS API bind address |
| `consul_rpc_bind_address` | *0.0.0.0* | RPC bind address |
| `consul_node_name` | `{{ inventory_hostname_short }}` | Node name (should not include dots) |
| `consul_recursors` | Empty list | List of upstream DNS servers — see [recursors](https://www.consul.io/docs/agent/options.html#recursors) | 
| `consul_bind_address` | dynamic from hosts inventory | The interface address to bind to
| `consul_dnsmasq_enable` | *false* | Whether to install and configure DNS API forwarding on port 53 using dnsmasq |
| `consul_iptables_enable` | *false* | Whether to enable iptables rules for DNS forwarding to Consul |
| `consul_acl_enable` | *false* | Enable ACLs |
| `consul_acl_datacenter` | *dc1* | ACL authoritative datacenter name |
| `consul_acl_default_policy` | *allow* | Default ACL policy |
| `consul_acl_down_policy` | *allow* | Default ACL down policy |
| `consul_acl_master_token` | *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL* | ACL master token — can be overridden with `CONSUL_ACL_MASTER_TOKEN` environment variable |
| `consul_acl_master_token_display` | *false* | Display generated ACL Master Token |
| `consul_acl_replication_token` | *SN4K3OILSN4K3OILSN4K3OILSN4K3OIL* | ACL replication token — can be overridden with `CONSUL_ACL_REPLICATION_TOKEN` environment variable|
| `consul_atlas_enable` | *false* | Enable Atlas support |
| `consul_atlas_infrastructure` | Environment variable | Atlas infrastructure name |
| `consul_atlas_token` | Environment variable | Atlas token |
| `consul_tls_enable` | *false* | Enable TLS |
| `consul_src_def` | `{{ role_path }}/files` | default source directory for TLS files |
| `consul_src_files` | `{{ role_path }}/files` | User specified source directory for TLS files, can be overridden with `CONSUL_SRC_FILES` environment variable |
| `consul_tls_dir` | `/etc/consul/ssl` | Target directory for TLS files, can be overridden with `CONSUL_TLS_DIR` environment variable |
| `consul_ca_crt` | `ca.crt` | CA certificate filename, can be overridden with `CONSUL_CA_CRT` environment variable |
| `consul_server_crt` | `server.crt` | Server certificate, can be overridden with `CONSUL_SERVER_CRT` environment variable |
| `consul_server_key` | `server.key` | Server key, can be overridden with `CONSUL_SERVER_KEY` environment variable |
| `consul_verify_incoming` | *false* | Verify incoming connections, can be overridden with `CONSUL_VERIFY_INCOMING` environment variable |
| `consul_verify_outgoing` | *true* | Verify outgoing connections, can be overridden with `CONSUL_VERIFY_OUTGOING` environment variable |
| `consul_verify_server_hostname` | *false* | Verify server hostname, can be overridden with `CONSUL_VERIFY_SERVER_HOSTNAME` environment variable |

### Host Inventory Variable

This role also uses a host inventory variable to define the server's role
when forming a cluster. One server should be designated as the bootstrap
server that uses the `bootstrap_expect` configuration directive, and the other
servers will connect to this server. You can also specify *client* as the
role, and Consul will be configured as a client agent instead of a server.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `consul_node_role` | NONE | Consul server role, one of: *bootstrap*, *server*, or *client* |

Here is an example of how the hosts inventory could be defined for a simple
cluster of 3 servers:

```
consul1.local consul_node_role=bootstrap 
consul2.local consul_node_role=server
consul3.local consul_node_role=server
```

### OS Distribution Variables

The `consul` binary works on most Linux platforms and is not distribution
specific. However, some distributions require installation of specific OS
packages with different package names.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `consul_centos_pkg` | `{{ consul_version }}_linux_amd64.zip` | Consul package filename |
| `consul_centos_url` | `{{ consul_zip_url }}` | Consul package download URL |
| `consul_centos_sha256` | SHA256 SUM | Consul download SHA256 summary |
| `consul_centos_os_packages` | list | List of OS packages to install |
| `consul_debian_pkg` | `{{ consul_version }}_linux_amd64.zip` | Consul package filename |
| `consul_debian_url` | `{{ consul_zip_url }}` | Consul package download URL |
| `consul_debian_sha256` | SHA256 SUM | Consul download SHA256 summary |
| `consul_debian_os_packages` | list | List of OS packages to install |
| `consul_redhat_pkg` | `{{ consul_version }}_linux_amd64.zip` | Consul package filename |
| `consul_redhat_url` | `{{ consul_zip_url }}` | Consul package download URL |
| `consul_redhat_sha256` | SHA256 SUM | Consul download SHA256 summary |
| `consul_redhat_os_packages` | list | List of OS packages to install |
| `consul_ubuntu_pkg` | `{{ consul_version }}_linux_amd64.zip` | Consul package filename |
| `consul_ubuntu_url` | `{{ consul_zip_url }}` | Consul package download URL |
| `consul_ubuntu_sha256` | SHA256 SUM | Consul download SHA256 summary |
| `consul_ubuntu_os_packages` | list | List of OS packages to install |

## Dependencies

Ansible requires GNU tar and this role performs some local use of the
unarchive module, so ensure that your system has `gtar` installed and
in the PATH.

If you're on system with a different (i.e. BSD) `tar`, like macOS and you
see odd errors during unarchive tasks, you could be missing `gtar`.

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

### Atlas Support

Basic support for Atlas is included in the role. You can set the environment
variables `CONSUL_ATLAS_ENABLE` to *true*, and also set the
`CONSUL_ATLAS_INFRASTRUCTURE` and `CONSUL_ATLAS_TOKEN` environment variables
to their correct values for your environment prior to executing your
playbook; for example:

```
CONSUL_ATLAS_ENABLE=true CONSUL_ATLAS_INFRASTRUCTURE=brianshumate/example \
CONSUL_ATLAS_TOKEN=00000000-000000000-000000000000-0000 \
ansible-playbook -i uat_hosts site.yml
```

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

You can enable TLS encryption by dropping a CA certificate, server certificate,
and server key into the role's `files` directory. By default these are named:

- `ca.crt` (can be overridden by {{ consul_ca_crt }})
- `server.crt` (can be overridden by {{ consul_server_crt }})
- `server.key` (can be overridden by {{ consul_server_key }})

Then either set the environment variable `CONSUL_TLS_ENABLE=true` or use the
Ansible variable `consul_tls_enable=true` when launching the role.

### Vagrant and VirtualBox

See `examples/README_VAGRANT.md` for details on quick Vagrant deployments
under VirtualBox for testing, etc.

## License

BSD

## Author Information

[Brian Shumate](http://brianshumate.com)

## Contributors

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTORS.md) for their
contributions to this project.

Contributions are welcome, provided that you can agree to the terms outlined
in [CONTRIBUTING.md](https://github.com/brianshumate/ansible-consul/blob/master/CONTRIBUTING.md)
