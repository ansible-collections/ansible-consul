# Consul

![](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)

This Ansible role performs a basic [Consul](https://consul.io/) installation,
including filesystem structure, example configuration, and Consul UI
installation.

It can also bootstrap a minimal development or evaluation cluster of 3 server
agents running in a Vagrant and VirtualBox based environment. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-consul/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

## Requirements

This role requires a Debian or RHEL based Linux distribution. It might work
with other software versions, but does work with the following specific
software and versions:

* Ansible: 2.1.2.0
* Consul: 0.7.0
* Debian: 8

## Role Variables

The role specifies variables in `defaults/main.yml` and `vars/*.yml`.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `consul_version` | `0.7.0` | Version to install |
| `consul_zip_url` | `https://releases.hashicorp.com/consul/{{ consul_version }}/consul_{{ consul_version }}_linux_amd64.zip` | Download URL |
| `consul_zip_sha256` | SHA256 SUM | Archive SHA256 summary |
| `consul_bin_path` | `/usr/local/bin` | Binary installation path |
| `consul_config_path` | `/etc/consul.d` | Configuration file path |
| `consul_data_path` | `/var/consul` | Data path |
| `consul_log_path` | `/var/log/consul` | Log path |
| `consul_user` | `consul` | OS user |
| `consul_group` | `bin` | OS group |
| `consul_datacenter` | `dc1` | Datacenter label |
| `consul_domain` | `consul` | Consul domain name |
| `consul_log_level` | `INFO` | Log level |
| `consul_syslog_enable` | `true` | Log to syslog |
| `consul_iface` | `eth1` | Consul network interface |
| `consul_bind_address` | `127.0.0.1` | Bind address |
| `consul_bootstrap_interface` | `{{ hostvars[groups['cluster_nodes'][0]]['ansible_eth1']['ipv4']['address'] }}` | The server interface that additional server nodes will join to for bootstrapping |
| `consul_dns_bind_address` | `127.0.0.1` | DNS API bind address |
| `consul_http_bind_address` | `0.0.0.0` | HTTP API bind address |
| `consul_https_bind_address` | `0.0.0.0` | HTTPS API bind address |
| `consul_rpc_bind_address` | `0.0.0.0` | RPC bind address |
| `consul_node_name` | `{{ inventory_hostname_short }}` | Node name (should not include dots) |
| `consul_bind_address` | dynamic from hosts inventory | The interface address to bind to
| `consul_dnsmasq` | `false` | Whether to install and configure DNS API forwarding on port 53 using dnsmasq |

### OS Distribution Variables

The Consul binary works on most Linux platforms and is not distribution
specific. Some distributions require installation of specific OS packages with different nomenclature, so this role has support for major Linux distributions.

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

None

## Example Playbook


After you have reviewed and altered any necessary variables, and created a
host inventory file, basic Consul installation is possible using the
included `site.yml` playbook example:

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

### NEW

The role now includes support for DNS forwarding with dnsmasq.

Enable like this:

```
ansible-playbook -i hosts site.yml --extra-vars "consul_dnsmasq=true"
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
