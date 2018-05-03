# GlusterFS Exercise

The goal of this exercise was to create a multi-node [GlusterFS](https://docs.gluster.org/en/latest/) storage cluster with sufficient fault tolerance to survive a single node failure with no loss of data.

## Getting Started

Please clone this repo to your local machine.

### Prerequisites

You will need the following on your local machine:
* [VirtualBox](https://www.virtualbox.org)
* [Ansible](https://www.ansible.com)
* [Vagrant](https://vagrantup.com)


### Installing

After cloning, simple `cd` into playbooks/ and issue `vagrant up`. 

You should see output similar to the following:

```
==> gfsn3: Checking for guest additions in VM...
==> gfsn3: Setting hostname...
==> gfsn3: Configuring and enabling network interfaces...
==> gfsn3: Running provisioner: ansible...
Vagrant has automatically selected the compatibility mode '2.0'
according to the Ansible version installed (2.5.1).

Alternatively, the compatibility mode can be specified in your Vagrantfile:
https://www.vagrantup.com/docs/provisioning/ansible_common.html#compatibility_mode

    gfsn3: Running ansible-playbook...

TASK [Configure Gluster volume.] ***********************************************
changed: [192.168.10.21]

TASK [Ensure Gluster volume is mounted.] ***************************************
changed: [192.168.10.23]
changed: [192.168.10.21]
changed: [192.168.10.22]

PLAY RECAP *********************************************************************
192.168.10.21              : ok=15   changed=12   unreachable=0    failed=0
192.168.10.22              : ok=14   changed=11   unreachable=0    failed=0
192.168.10.23              : ok=14   changed=11   unreachable=0    failed=0
```
To test the cluster:

`ansible gluster -i hosts -a "gluster peer status" -b`

You should see output similar to the following:

```
192.168.10.21 | SUCCESS | rc=0 >>
Number of Peers: 2

Hostname: 192.168.10.22
Port: 24007
Uuid: ccce5e62-2f5c-4d43-978a-757134180152
State: Peer in Cluster (Connected)

Hostname: 192.168.10.23
Port: 24007
Uuid: e3878ff5-d1fa-40d8-8708-e3b8a15079de
State: Peer in Cluster (Connected)

192.168.10.23 | SUCCESS | rc=0 >>
Number of Peers: 2

Hostname: 192.168.10.21
Port: 24007
Uuid: 6402aa0c-1926-491a-8629-af3d5484b64e
State: Peer in Cluster (Connected)
```

Data for your GlusterFS resides in /data/gluster on each node. 
Simply
 `sudo touch /data/gluster/free_as_in_beer` from any node or to use Ansible to accomplish this on all nodes:

 `ansible gluster -i hosts -a "touch /data/gluster/free_as_in_beer" -b`

 Destroying a sample node can be done with:

`vagrant destroy gfsn[n]` where `n` is a sample node number.

To view general volume info:

`ansible gluster -i hosts -a "gluster volume info" -b`

Additionally, if you are testing operations for volume healing within a cluster:

`ansible gluster -i hosts -a "gluster volume heal gluster info" -b`

If you'd like to test for idempotency:

`ansible-playbook -i hosts gluster-cluster.yml -b`

## Authors

* **Derek Monaghan** - *Initial work* - [dmonagha](https://github.com/dmonagha)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details