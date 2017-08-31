# Ansible Role : lxd-networks

Creation of the networks on a remote lxd.

Creation of the vxlan bond between the ansible host and the lxd.

Second stage to enable the creation of containers.

## Requirements

- A working installation of LXD local and distant
- A network connection between LXDs with _vxlan-port_ (default is 8472) connection available from local to remote
- A ssh connection from local to remote

## Inventory

- need a _Container ship(s)_ group, which will be the hosts of the role
- each host in this group need to be detailled as such:
  - _alias-or-hostname_ [ ansible_host=_ipaddress-or-else_ ]

## Role Variables

Variable for the role are:

- lxd_networks_local_hostname: hostname of the local ansible host
- lxd_networks_local_ip: ip address of the local ansible host
- lxd_networks : List of networks objects
  - name: the name of the network
  - mode: vlan/local
  - interface_phy: the physical interface to connect to (used to create a vlan interface in vlan mode)
  - vlan_id: mandatory for vlan mode
  - vxlan: Id of the vxlan tunnel between lxd host and ansible host
  - description: useless at now
  - routing: useless at now

### 2 modes : vlan / local

- Local mode is for local network with private network
  - it can be connected to real network via an interface_phy
  - it can be accessed via the vxlan tunnel from the ansible host
  - its containers have an automatic dhcp address
- vlan mode is for physic/public network 
  - its dhcp is off
  - it has to be connected with a vlan interface (eg : eth0.234 vlan 234 on interface eth0)
  - its container need to have a static ip, in ansible its needed to set this address via lxd_connection


## Example playbook

### Inventory

```
[containerships]
clementine ansible_host=2.16.3.168
gudrun     ansible_host=cs.maersk.com
```

### Playbook

```
- hosts: containerships
  roles:
    - jadjay.lxd-networks
```

### Variables

*File: group_vars/containerships.yml*
```
lxd_networks_local_hostname: bolwerk
lxd_networks_local_ip: 12.18.42.23
lxd_networks:
  - name: baarwerk42
    mode: vlan
    interface_phy: eth0
    vlan_id: 42
    vxlan: 1233
    description: "Zone nummer 42"
  - name: lokaalwerk
    mode: local
    vxlan: 1234
    description: "Lokaal prive netwerk"
    routing: true
    interface_phy: eth1
  - name: private
    mode: local
    vxlan: 3514
    description: "bundel netwerk"
    routing: false
    interface_phy: false
```

# License

GNU/GPLv3

# Author Information

This role was created in 2017 by Jerome Avond.

