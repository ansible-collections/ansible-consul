# Consul

![](https://travis-ci.org/brianshumate/ansible-consul.svg?branch=master)

This Ansible role performs a basic [Consul](https://consul.io/) installation,
including filesystem structure, example configuration, and Consul UI
installation.

It will also bootstrap a minimal cluster of 3 server nodes and do this
in a development environment based on Vagrant and VirtualBox. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-consul/blob/master/examples/README_VAGRANT.md) for more details about the developer mode setup.

## Requirements

This role requires a Debian or RHEL based Linux distribution. It might work
with other software versions, but does work with the following specific
software and versions:

* Ansible: 2.1.0.0
* Consul: 0.6.4
* Debian: 8

## Role Variables

The role specifies variables in `defaults/main.yml` and `vars/*.yml`.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `consul_version` | `0.6.4` | Consul version to install |
| `consul_zip_url` | `https://releases.hashicorp.com/consul/{{ consul_version }}/consul_{{ consul_version }}_linux_amd64.zip` | Consul download URL |
| `consul_zip_sha256` | SHA256 SUM | Consul download SHA256 summary |
| `consul_bin_path` | `/usr/local/bin` | Consul binary installation path |
| `consul_config_path` | `/etc/consul.d` | Consul configuration file path |
| `consul_data_path` | `/var/consul` | Consul data path |
| `consul_log_path` | `/var/log/consul` | Consul log path |
| `consul_user` | `consul` | Consul OS user |
| `consul_group` | `bin` | Consul OS group |
| `consul_datacenter` | boone | Consul datacenter label |
| `consul_domain` | `local` | Consul domain name |
| `consul_log_level` | `INFO` | Consul logging level |
| `consul_syslog_enable` | true | Consul logs to syslog |
| `consul_ui_url` | `https://dl.bintray.com/mitchellh/consul/0.5.2_web_ui.zip` | Consul UI download URL |
| `consul_ui_pkg` | `0.5.2_web_ui.zip` | Conul UI package file name |
| `consul_ui_sha256` | SHA256 SUM | Consul UI download SHA256 summary |
| `consul_iface` | `eth1` | Consul network interface |
| `consul_bind_address` | dynamic from hosts inventory | The interface address to bind to

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
