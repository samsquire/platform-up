# platform-up

This tool brings up realistic clusters using LXC containers and runs configuration
management against them.


# quickstart

```
# Install vagrant
ansible -c local -i localhost, -b -m apt localhost -a deb=https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb

# Installation
platform-up install

```
