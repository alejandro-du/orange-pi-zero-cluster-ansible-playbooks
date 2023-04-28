# Ansible playbooks to manage Orange Pi Zero 2 clusters

This repository contains a set of Ansible playbooks that help to set up and manage an Orange Pi Zero 2 cluster.

## How to run?

Install Ansible on a control node and define an inventory (**/etc/ansible/hosts**). For example:

```
[opiesz]
192.168.1.101		ansible_user=orangepi		hostname=ozpi01
192.168.1.102		ansible_user=orangepi		hostname=ozpi02
192.168.1.103		ansible_user=orangepi		hostname=ozpi03
192.168.1.104		ansible_user=orangepi		hostname=ozpi04

Run a playbook as follows:

```bash
ansible-playbook some-playbook.yml --ask-pass
```

Or:

```bash
ansible-playbook some-playbook.yml --ask-pass --ask-become-pass
```

## Configuring

Change the target group name (the default is `opiesz`) in the **.yml** files before running a playbook if needed.

## Available playbooks

* **configure-hosts.yml**: Sets host names
* **ping.yml**: Pings all the nodes
* **upgrade.yml**: Updates and upgrades the nodes
* **reboot.yml**: Reboots all the nodes
* **shutdown.yml**: Shuts down all the nodes
* **temperature.yml**: Prints the temperature of each machine
* **docker.yml**: Installs Docker
* **docker-swarm.yml**: Creates a docker swarm

## Deploying MariaDB

* **mariadb-stack**: Deploys a MariaDB replication topology with MaxScale

See https://github.com/alejandro-du/mariadb-docker-deployments/tree/armv7
