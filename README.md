# platform-up

This tool answers the question of how do you bring up your entire platform on one workstation. With Vagrant, LXC containers and [vagrant-lxc](https://github.com/fgrehm/vagrant-lxc).

platform-up is the name of an all singing and dancing tool I wrote while at a client. This repository is more of the pattern right now, with a simpler implementation.

The problem it solves is that you have lots of Ansible or Chef code and you want to test it together before pushing. This is for large codebases where you have lots of Ansible codebases with lots of hosts and Vagrant is the wrong tool to run it against your containers.

This tool uses Vagrant to bring up clusters of LXC containers and then runs your configuration management against them so you can test that your CM code works together before you push. This project uses Vagrant with vagrant-lxc.

# Features

* **Change detection** platform-up only runs Ansible pipelines when the Ansible code has changed or the platform-up.yml file has changed.
* **Host limitation** Some ansible projects only need to run against certain hosts, this tool lets you override the hosts being ran for each Ansible project.

# setup

These setup instructions assume Ubuntu.

Install python3 and python3-pip

```
sudo apt install python3 python3-pip
```

Install dependencies

```
pip3 install -r requirements.txt
```

Install vagrant
```
ansible -c local -i localhost, -b -m apt localhost -a deb=https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb
```
Install lxc
```
sudo apt install lxc
```

Create a LXC base box called "bionic", defaulting to Ubuntu bionic (see script to create other ubuntu versions) This script adds it to Vagrant too
```
sudo ./create-base-box bionic
```
Installation - installs vagrant-lxc and vagrant-hostmanager - after this command, platform-up will be on your PATH after you log out and log back in
```
./platform-up install 
```
In your sourcecode directory where your Ansible or CM code is, create our vagrant hosts based off the LXC container we created above
```
vagrant init bionic
```
## Vagrantfile configuration
You need to add the following to the generated Vagrantfile.

```
  config.ssh.insert_key = false
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
```

This tool uses Vagrant multimachine to bring up each node in your environment:

```
  config.vm.define "app01" do |node|
    node.vm.hostname = "app01"
  end
  config.vm.define "haproxy01" do |node|
    node.vm.hostname = "haproxy01"
  end  
```

# Configure platform up

Platform up works by looping over your project file and running Ansible or Chef in the directory.

 * **Source directories** a list of directories that have configuration management code in them
 * **Hosts** a list of hosts

[See this file for an example that is used to test playbooks.](https://github.com/samsquire/devops-pipeline-starter/blob/master/platform-up.yml)

```
hosts:
- app01
- haproxy01

- name: "path to ansible playbook"
  playbook: "playbook filename"
  type: "ansible"
  user: SSH user (usually vagrant)
  hosts:
    - a list of hosts to run this ansible playbook against if not all of them
```

Create an inventory file **at the same place as your platform-up.yml**. This file should contain all your hosts that you created in Vagrant above. Platform-up does not generate this file in case you have properties to override.

# Usage

To run all your projects, then run:

```
platform-up go
```





