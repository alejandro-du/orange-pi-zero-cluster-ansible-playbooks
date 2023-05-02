# Ansible playbooks to manage Orange Pi 3 LTS clusters

This repository contains a set of Ansible playbooks that help to set up and manage an Orange Pi 3 LTS cluster.

## How to run?

Install Ansible on a control node and define an inventory (**/etc/ansible/hosts**). For example:

```
[opies]
192.168.1.101		ansible_user=orangepi		hostname=opi01
192.168.1.102		ansible_user=orangepi		hostname=opi02
192.168.1.103		ansible_user=orangepi		hostname=opi03
192.168.1.104		ansible_user=orangepi		hostname=opi04
```

Run a playbook as follows:

```bash
ansible-playbook some-playbook.yml --ask-pass
```

Or:

```bash
ansible-playbook some-playbook.yml --ask-pass --ask-become-pass
```

## Configuring

Change the target group name (the default is `opies`) in the **.yml** files before running a playbook if needed.

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
