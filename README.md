# Ansible playbooks to manage Orange Pi Zero 2 clusters

This repository contains a set of Ansible playbooks that help to set up and manage an Orange Pi Zero 2 cluster.

## How to run?

Install Ansible on a control node and define an inventory (**/etc/ansible/hosts**). For example:

```cfg
[opies]
192.168.1.101		ansible_user=orangepi		hostname=ozpi01	master=true
192.168.1.102		ansible_user=orangepi		hostname=ozpi02
192.168.1.103		ansible_user=orangepi		hostname=ozpi03
192.168.1.104		ansible_user=orangepi		hostname=ozpi04
```

Run a playbook as follows:

```sh
ansible-playbook some-playbook.yml --ask-pass
```

Or:

```sh
ansible-playbook some-playbook.yml --ask-pass --ask-become-pass
```

## Configuring

Change the target group name (the default is `opies`) in the **.yml** files before running a playbook if needed.

## General usage playbooks

* **configure-hosts.yml**: Sets host names
* **ping.yml**: Pings all the nodes
* **upgrade.yml**: Updates and upgrades the nodes
* **reboot.yml**: Reboots all the nodes
* **shutdown.yml**: Shuts down all the nodes
* **temperature.yml**: Prints the temperature of each machine
* **docker.yml**: Installs Docker

## Deploying MariaDB using Docker Swarm

* **docker-swarm.yml**: Creates a Docker Swarm cluster
* **mariadb-stack**: Deploys a MariaDB replication topology with MaxScale

See https://github.com/alejandro-du/mariadb-docker-deployments/tree/armv7

## Deploying MariaDB on Kubernetes (K3s)

* **k3s.yml**: Creates a Kubernetes cluster (K3s)
* **Mariadb-k8s-operator.yml**: Installs the MariaDB Kubernetes Operator

If you want to use `kubectl` from a separate machine (like your laptop), you need to copy the **/etc/rancher/k3s/k3s.yaml** file from your master node to your local `kubectl` config file with the IP changed to the master node IP. For example:

```sh
ssh -tt orangepi@192.168.1.161 "sudo cat /etc/rancher/k3s/k3s.yaml" | cat > ~/.kube/config
sed -i -e 's/127.0.0.1/192.168.1.161/g' ~/.kube/config
```

* _You have reintroduce the password after hitting ENTER in the previous example even though there's no prompt._

### Deploy Single MariaDB node

Deploy a single MariaDB server as follows:

```sh
kubectl apply -f k8s-deployments/single-mariadb-instance.yml
```

Check the progress with:

```sh
kubectl get pods -w
```

### 3-node MariaDB Galera cluster

Deploy a 3-node [Galera](https://mariadb.com/kb/en/galera-cluster/) multi-master cluster (using Galera) as follows:

```sh
kubectl apply -f k8s-deployments/mariadb-galera-cluster.yml
```

### Connect to the database

After a successful deployment, test the connection as follows (use the IP address of any of your Orange Pi devices):

```sh
mariadb -h 192.168.1.101 --port 30001 -u root -pdemo123
````

If you are using a [MariaDB Connector](https://mariadb.com/kb/en/connectors/) (Java, Python, Node.js, C++), check the HA modes where you can specify a list of IP addresses to let the connector [automatically perform failover (and transaction reply)](https://www.youtube.com/watch?v=y8eLffT9-8Q) for you.
