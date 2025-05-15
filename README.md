# Ansible playbooks to manage Orange Pi Zero 2 clusters

This repository contains a set of Ansible playbooks that help to set up and manage an Orange Pi Zero 2 cluster (lab, not for production). It also contains MariaDB deployments for testing and demo purposes, including [MariaDB Server](https://mariadb.com/kb/en/documentation/), [MariaDB MaxScale](https://mariadb.com/kb/en/maxscale/), and [MariaDB cluster (Galera)](https://mariadb.com/kb/en/galera-cluster/).

## How to run?

Install Ansible on a control node and define an inventory (**/etc/ansible/hosts**). For example:

```cfg
[opies]
192.168.1.101		ansible_user=orangepi		hostname=opi01	master=true
192.168.1.102		ansible_user=orangepi		hostname=opi02
192.168.1.103		ansible_user=orangepi		hostname=opi03
192.168.1.104		ansible_user=orangepi		hostname=opi04
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

Change the target group name by setting the `TARGET_GROUP_NAME` environment variable in your OS before running playbooks if needed. The default group name is `opies`.

## General usage playbooks

* **configure-hosts.yml**: Sets host names, LED activity, CPU max speed, installs Avahi
* **ping.yml**: Pings all the nodes
* **upgrade.yml**: Updates and upgrades the nodes
* **reboot.yml**: Reboots all the nodes
* **shutdown.yml**: Shuts down all the nodes
* **temperature.yml**: Prints each node's temperature
* **docker.yml**: Installs Docker

## Deploying MariaDB using Docker Swarm

* **docker-swarm.yml**: Creates a Docker Swarm cluster
* **mariadb-stack**: Deploys a MariaDB replication topology with a [MaxScale proxy](https://mariadb.com/kb/en/maxscale/) in front

See https://github.com/alejandro-du/mariadb-docker-deployments/tree/armv7

## Deploying MariaDB on Kubernetes (K3s)

* **k3s.yml**: Creates a Kubernetes cluster (K3s)
* **Mariadb-k8s-operator.yml**: Installs the MariaDB Kubernetes Operator

### Configure kubectl

If you want to use `kubectl` from a separate machine (like your laptop), you need to copy the **/etc/rancher/k3s/k3s.yaml** file from your master node to your local `kubectl` config file with the IP changed to the master node IP. For example:

```sh
ssh -tt orangepi@192.168.1.161 "sudo cat /etc/rancher/k3s/k3s.yaml" | cat > ~/.kube/config
sed -i -e 's/127.0.0.1/192.168.1.161/g' ~/.kube/config
```

* _You have to re-introduce the password a second time in the previous example even though there's no prompt for it._

### Create a secret for the MariaDB root password

Take note of the password:

```sh
kubectl create secret generic root-password --from-literal=password=demo123
```

### Deploy a single MariaDB node

Deploy a single MariaDB server as follows:

```sh
kubectl apply -f k8s-deployments/single-mariadb-instance.yml
```

Check the progress with:

```sh
kubectl get pods -o wide -w
```

### Deploy a 3-node MariaDB cluster (with Galera)

Make sure you "undeploy" previously deployed single MariaDB instances:

```sh
kubectl delete -f k8s-deployments/single-mariadb-instance.yml
kubectl delete pvc -l app.kubernetes.io/name=mariadb
```

Deploy a 3-node [Galera](https://mariadb.com/kb/en/galera-cluster/) multi-master cluster (using Galera) as follows:

```sh
kubectl apply -f k8s-deployments/mariadb-galera-cluster.yml
```

Check the progress with:

```sh
kubectl get pods -o wide -w
```

### Connect to the database

After a successful deployment, test the connection as follows (use the IP address of any of your Orange Pi devices):

```sh
mariadb -h 192.168.1.101 --port 30001 -u root -pdemo123
````

If you are using a [MariaDB Connector](https://mariadb.com/kb/en/connectors/) (Java, Python, Node.js, C++), check the HA modes where you can specify a list of IP addresses to let the connector [automatically perform failover (and transaction reply)](https://www.youtube.com/watch?v=y8eLffT9-8Q) for you.
