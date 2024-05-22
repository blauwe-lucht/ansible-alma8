# Ansible AlmaLinux & CentOS7 demo

This repo is part of a workshop to teach people Ansible.

The Ansible control server is AlmaLinux 8, there are two nodes, a CentOS 7 node and an AlmaLinux 8 node.

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
ansible-playbook playbook.yml -v
```

## Using vscode to edit Ansible code

To make proper use of the vscode Ansible extension:

### Install the vscode Remote-SSH extension

Click on the extensions icon (or press Ctrl-Alt-X). Search for Remote-SSH. On the [Remote - SSH extension by Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) click 'Install'.

### Set SSH configuration

Change or create the file ~/.ssh/config and add

``` text
Host acs
  HostName 127.0.0.1
  Port 21556
  User vagrant
  IdentityFile "<path to your git directory>\ansible-alma8\.vagrant\machines\acs\virtualbox\private_key"
```

You can check if the config is working properly by openen a prompt and typing

``` bash
ssh acs
```

If you're logged in, everything works fine.

### Connect to acs VM

In the bottom left, press the green connection button (it looks a bit like '><').
Select 'Connect to Host...', select 'acs'. When asked for an operating system type, select 'Linux'.

Vscode will now install some software on the VM.

### Install extensions

Click on the extensions icon (or press Ctrl-Alt-X). Search for Ansible. On the [Ansible extension](https://marketplace.visualstudio.com/items?itemName=redhat.ansible by RedHat) click 'Install in SSH: acs'.
Another handy extension to install is [Ansible Go to Definition](https://marketplace.visualstudio.com/items?itemName=BlauweLucht.ansible-go-to-definition).

### Open the project folder

In the File menu select 'Open Folder...'. Type /vagrant and click OK or press Enter. Trust the directory.

You can now write Ansible code with syntax highlighting, code completion, linting and navigation.
