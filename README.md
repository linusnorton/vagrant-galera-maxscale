# vagrant-galera-maxscale

A Galera cluster (MariaDB 10.1) and MaxScale proxy set up on Ubuntu 15.10 using Vagrant.

`Disclaimer: this is for testing purposes only. Some of this configuration should not be used in production.`

## Requirements

Vagrant, virtualbox and ansible.

## Usage

```
vagrant up
mysql -h 10.0.0.100 -P 4306 -umaxscale -pdefaultmaxscalepassword
```

## Explanation

Vagrant creates three cluster nodes and a MaxScale proxy. All requests should be sent to the proxy. When a node goes down it will be removed from the proxy.

Some of the Ansible configuration is a bit clunky because starting the MariaDB instances needs to be done in a specific manner. First the primary node needs to be bootstrapped using the `galera_new_cluster` command and then the remaining nodes can be started.
