# geck

![Garden of Eden Creation Kit](geck.png)

Raspberry pi based Kubernetes cluster provisioned with Ansible

based on https://github.com/ljfranklin/k8s-pi

## The idea
As single board computers like Raspberry Pi or the LattePanda get more and more popular, more people is moving their homelab solutions to use these because they are cheaper, more energy efficient and more quiet. (Also its fun). The idea is to use a set of Ansible playbooks to provision N Raspberry Pi to run Kubernetes cluster and its necessary services. Later the cluster can be used to various applications in the HomeLab.

> Please do not use this to provision production environments, this is only meant to be used for experiemental reason in a Homelab setting

## The cluster
The Cluster will be provisioned to use Kubernetes with the Traefik ingress controller and MetalLB as baremetal loadbalancer. Cert-manager will also be deployed to the cluster so later SSL certificates can be provisioned automatically. NFS-provisioner is deployed to automatically provision PV as we spin more services.

## Playbooks

__bootstrap.yml__

This is the first thing you run if you want to deploy a fresh cluster, you can also run this to remove the current deployment and create fresh cluster. You should be able to run this over and over again on the same cluster without re-imaging.

__update.yml__

This is the playbook to run when you want to update existing cluster to a latest version, you can run this over and over again to update the infrasture (ie. kubernetes)

__deploy.yml__

This is to run on existing cluster once you already have the necessary services deployed. It deploys all the useful services for the cluster

## Using the playbooks
Usually, you can just run `bootstrap.yml`, it will include all the rest playbooks. The order will be

bootstrap -> update -> deploy

## Preparing the SD card
The project include scripts from the [ljfranklin/k8s-pi](https://github.com/ljfranklin/k8s-pi) that creates SD cards ready to be run in raspberry pi using HypriotOS. This also makes SD card that will make the Raspberry Pi to use cloud-init to do initial setup in a headless way. To provision SD cards run:

```
/pi/provision.sh -d /dev/sda -n k8s-node1 -p "$(kr me)" -i 192.168.1.100
```

* -d Your SD card device path
* -p Your SSH public key
* -n Hosename
* -i The IP address

## Run it

```
ansible-playbook -i inventory/hosts.ini --extra-vars @secrets/secrets.yml bootstrap.yml
```

## Included services
Includes services are ones that is installed when you run `bootstramp.yml` I believe anyone using the cluster will need it so they are installed by default.
- K3s with traefik
- Metallb
- Cert Manager
- NFS Provisioner
- Cloudflare DDNS
- Gitea
- Kube dashboard
- OpenVPN

## Custom services
Custom services are ones that is unique to my use case and might not be needed by you, also onece that might require some user interactiction before it can be used.
- UnifiPoller
- InfluxDb
- Minecraft
- Chronograph
- Drone (needs user interaction - oauth creds)
- Docker registry
- Owncloud
- Proxy for other internal service
