# platform-up

This tool brings up clusters of LXC containers and runs your configuration management against them so you can test that your CM code works before you push. This project uses Vagrant and vagrant-lxc.

# quickstart

```
# Install vagrant
ansible -c local -i localhost, -b -m apt localhost -a deb=https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb

# Create a LXC base box, defaulting to Ubuntu bionic
sudo ./create-base-box bionic

# Installation
platform-up install

```

