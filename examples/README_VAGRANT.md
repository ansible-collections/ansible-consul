# Consul with Ansible

This project provides documentation and a collection of scripts to help you
automate the deployment of Consul using
[Ansible](http://www.ansibleworks.com/). These are the instructions for
deploying a development cluster on Vagrant and VirtualBox.

The documentation and scripts are merely a starting point designed to both
help familiarize you with the processes and quickly bootstrap an environment
for development. You may wish to expand on them and customize
them with additional features specific to your needs later.

## Vagrant Development Cluster

In some situations deploying a small cluster on your local development
machine can be handy. This document describes such a scenario using the
following technologies:

* [Consul](https://consul.io)
* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](http://www.vagrantup.com/) with Ansible provisioner and
  supporting plugin
* [Ansible](http://www.ansibleworks.com/)

Each of the virtual machines for this guide are configured with
1.5GB RAM, 2 CPU cores, and 2 network interfaces. The first interface uses
NAT and has connection via the host to the outside world. The second
interface is a private network and is used for Consul intra-cluster
communication in addition to access from the host machine.

The Vagrant configuration file (`Vagrantfile`) is responsible for
configuring the virtual machines and a baseline OS installation.

The Ansible playbooks then further refine OS configuration, perform Consul
software download and installation, and the initialization of nodes
into a ready to use cluster.

## Designed for Ansible Galaxy

This role is designed to be installed via the `ansible-galaxy` command
instead of being directly run from the git repository.

You should install it like this:

```
ansible-galaxy install brianshumate.consul
```

You'll want to make sure you have write access to `/etc/ansible/roles/` since
that is where the role will be installed by default, or define your own
Ansible role path by creating a `$HOME/.ansible.cfg` file with these contents:

```
[defaults]
roles_path = PATH_TO_ROLES
```

Change `PATH_TO_ROLES` to a directory that you have write access to.

## Quick Start

Begin from the top level directory of this project and use the following
5 steps to get up and running:

1. Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads), [Vagrant](http://downloads.vagrantup.com/), [vagrant-hosts](https://github.com/adrienthebo/vagrant-hosts), and [Ansible](http://www.ansibleworks.com/docs/intro_installation.html#latest-releases-via-pip).
2. Edit `/etc/hosts` or use the included `bin/preinstall` script to add
   the following entries to your development system's `/etc/hosts` file:
 * 10.1.42.210 consul1.local consul1
 * 10.1.42.220 consul2.local consul2
 * 10.1.42.230 consul3.local consul3
3. cd `$PATH_TO_ROLES/brianshumate.conusul/examples`
4. `vagrant up`
5. Access the cluster web UI at http://consul1.local:8500/ui/

By default, this project will install Debian based cluster nodes. If you
prefer, it can also install CentOS 7 based nodes by changing the command
in step 4 to the following:

```
BOX_NAME="chef/centos-7.0" vagrant up
```

## Notes

0. This project functions with the following software versions:
  * Consul version 0.6.4
  * Ansible version 2.0.0.2
  * VirtualBox version 5.0.24
  * Vagrant version 1.8.1
  * Vagrant Hosts version 2.8.0
1. This project uses Debian Jessie by default, but you can choose other OS
   with the *BOX_NAME* environment variable
2. The `bin/preinstall` shell script performs the following actions for you:
 * Adds each node's host information to the host machine's `/etc/hosts`
 * Optionally installs the Vagrant hosts plugin
3. **vm: The '' provisioner could not be found.** error - make sure you have
   vagrant-hosts plugin installed

## References

1. https://www.consul.io/
2. http://www.ansible.com/
3. http://www.vagrantup.com/
4. https://www.virtualbox.org/
5. https://github.com/adrienthebo/vagrant-hosts
