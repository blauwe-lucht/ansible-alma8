# Ansible AlmaLinux & CentOS7 demo

This repo is part of a workshop to teach people Ansible.

## Prerequisites

- VirtualBox, tested with 7.0.14
- Vagrant, tested with 2.4.1

## Usage

Create the VMs:

``` bash
vagrant up
```

Run Ansible to configure the nodes:

``` bash
vagrant ssh acs
cd /vagrant/ansible
ansible-playbook playbook -v
```
