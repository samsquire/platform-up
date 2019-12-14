# platform-up

This repository is more of a pattern than a tool at the moment. The problem it solves is that you have lots of Ansible or Chef code and you want to test it together before pushing. This is for large codebases where you have lots of Ansible codebases with lots of hosts and Vagrant is the wrong tool to run it against your containers.

This tool uses Vagrant to bring up clusters of LXC containers and then runs your configuration management against them so you can test that your CM code works together before you push. This project uses Vagrant and vagrant-lxc.

# setup

```
# Install vagrant
ansible -c local -i localhost, -b -m apt localhost -a deb=https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb

# Install lxc
sudo apt install lxc

# Create a LXC base box called "bionic", defaulting to Ubuntu bionic (see script to create other ubuntu versions) This script adds it to Vagrant too
sudo ./create-base-box bionic

# Installation - after this command, platform-up will be on your PATH after you log out and log back in
./platform-up install 

# In your sourcecode directory where your Ansible or CM code is, create a vagrant box based off the LXC container
vagrant init bionic
```

You need to add the following to the generated Vagrantfile.

```
  config.ssh.insert_key = false
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
```

Add all your machines to your Vagrantfile

```
  config.vm.define "app01" do |node|
    node.vm.hostname = "app01"
  end
  config.vm.define "haproxy01" do |node|
    node.vm.hostname = "haproxy01"
  end  
```

Configure platform-up with:

 * a list of directories that have configuration management code in them
 * a list of hosts

```
hosts:
- app01
- haproxy01

projects:
- name: "ansible-local/playbooks/haproxy"
  playbook: "haproxy.playbook.yml"
  type: "ansible"
  user: vagrant
  hosts:
    - haproxy01

```

Change "inventory" to be an ansible inventory of your hosts. Platform-up does not generate this file in case you have properties to override.

Now run

```
platform-up go
```
