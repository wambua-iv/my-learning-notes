idrac password =>
ps => Zain@@@2020


## uplink commands

#(config) interface ethernet 1/1/25
#(conf-if-eth1/1/25) description "Uplink to core
switching"
#(conf-if-eth1/1/25) no shutdown
#(conf-if-eth1/1/25) channel-group 40 mode active
#(conf-if-eth1/1/25) no switchport
#(conf-if-eth1/1/25) flowcontrol receive off
#(conf-if-eth1/1/25) flowcontrol transmit off
#(conf-if-eth1/1/25) exit
#(config) interface ethernet 1/1/26
#(conf-if-eth1/1/26) description "Uplink to core
switching"
#(conf-if-eth1/1/26) no shutdown
#(conf-if-eth1/1/26) channel-group 40 mode active
#(conf-if-eth1/1/26) no switchport
#(conf-if-eth1/1/26) flowcontrol receive off
#(conf-if-eth1/1/26) flowcontrol transmit off
#(conf-if-eth1/1/26) end
#######################################
<ghj>


Management Switch
# configure terminal
# hostname MgmtSwitch
# interface vlan 1501
# description Management_network
# no shutdown
# exit

===================================
# interface mgmt 1/1/1
# no shutdown
# no ip address dhcp
# ip address 172.17.57.10/24
# exit

# management route 0.0.0.0/0 172.17.57.254
# ntp server 172.17.57.50
====================================
Management port

# interface ethernet 1/1/1
# description “<>"
# switchport mode access
# switchport access vlan "<>"
# no shutdown
# exit

===================================
Port Channel for Internet Uplink

# interface port-channel 40
# description "<>"
# no shutdown
# switchport mode trunk
# switchport access vlan 1
# switchport trunk allowed vlan 1501
# exit

==================================
Uplink Ports

# interface ethernet ././.
# description "<>"
# no shutdown
# channel-group 40 mode active
# no switchport
# flowcontrol receive off
# flowcontrol transmit off
# exit


# interface ethernet ././.
# description "<>"
# no shutdown
# channel-group 40 mode active
# no switchport
# flowcontrol receive off
# flowcontrol transmit off
# end

###################################################
ToR Config

# configure terminal
# hostname Data_Switch_1


completed step

# interface mgmt 1/1/1
# no shutdown
# no ip address dhcp
# ip address 172.17.57.20/24
# exit
----------------------------
# management route 0.0.0.0/0 172.17.57.254
# ntp server 172.17.57.50

================================ confirm with network Admin
<Spanning tree config> * inquire from network admin priority
# spanning-tree mode rstp
# spanning-tree rstp priority 40960
# end
--------------------------------
creating VLANs
--
# interface vlan 1
# description Cluster_Network
# no shutdown
# exit

------------------------------
VLT Ports

# interface range ethernet ././. - ././.
# description "<>"
# no switchport
# no shutdown
# exit
--------------
# vlt-domain <ID>
# discovery-interface ethernet ././. - ././.
# back-up destription <IP address of the other switch>

# peer-routing
# primary-priority 1
# vlt-mac 00:00:00:00:00:01
# end

-------------------------------------------

ToR Port Channel for PowerStore Cluster Network

Node A Port Channel ### create port channels for all the Nodes
-------------------
# interface port-channel 20
# description <>
# vlt-port-channel 20
# no shutdown
# switchport mode trunk
# switchport access vlan 1
# spanning-tree port type edge
# mtu 9216
# exit
-------------------

Node B port channel
# interface port-channel 30
# description <>
# vlt-port-channel 30
# no shutdown
# switchport mode trunk
# switchport access vlan 1
# mtu 9216
# spanning-tree port type edge
# exit
--------------------

Activate Interfaces for Port channel

# interface ethernet 1/1/1
# no shutdown
# no switchport
# channel-group 20 mode active
# mtu 9216
# flowcontrol receive off
# flowcontrol transmit off
# exit


# interface ethernet 1/1/2
# no shutdown
# no switchport
# channel-group 30 mode active
# flowcontrol receive off
# flowcontrol transmit off
# end
----------------------------
Top of Rack UpLink

# interface port-channel 50
# description "<>"
# no shutdown
# switchport mode trunk
# switchport access vlan 1
# switchport trunk allowed vlan 1
# vlt-port-channel 50
# exit

-----------------------------
Activate Port For Uplink

# interface ethernet 1/1/
# description Uplink_port
# no shutdown
# channel-group 50 mode active
# no switchport
# flowcontrol receive off
# flowcontrol transmit off
# exit


# intereface ethernet 1/1/
# description Uplink_port
# no shutdown
# channel-group 50 mode active
# no switchport
# flowcontrol receive off
# flowcontrol transmit off
# end



# copy running-configuration startup-configuration
# exit
=================================================================

show interface port-channel brief
show lacp neighbor
show interface status
show port-channel summary


==================================================================================
volume mapping

# cat /sys/class/fc_host/host*/port_name
# sudo apt install sysfsutils, scsitools
# systool -c fc_host -v
# sudo rescan-scsi-bus

# lsblk 
# sudo apt install multipath-tools
# sudo systemctl enable --now multipathd
# sudo mpathconf --enable --with_multipathd y
# sudo multipath -ll

Make file system 
# sudo fdisk /dev/mapper/mpathX   
# sudo mkfs.ext4 /dev/mapper/mpathXp1

# sudo mkdir -p /mnt/fc_lun
# sudo mount /dev/mapper/mpathXp1 /mnt/fc_lun

persist mount point 
# sudo blkid /dev/mapper/mpathXp1
# UUID=your-uuid-here /mnt/fc_lun ext4 defaults 0 2


-------------------------------------------------------

set up networking

# ip link 

configure `/etc/netplan/00-installer-config.yaml`

network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s25:
      addresses:
        - 192.168.0.100/24
      routes:
        - to: default
          via: 192.168.0.1
      nameservers:
          search: [mydomain, otherdomain]
          addresses: [1.1.1.1, 8.8.8.8, 4.4.4.4]
      
      

sudo netplan apply


sudo ethtool eth4
sudo lshw -class network
========================
Look at the configuration: line — especially:
driver=... → is a driver loaded?
link=no → confirms no link
capabilities: ... → should include ethernet
If it says UNCLAIMED, that means there's no driver.
========================
sudo ip link set <> up
ip addr show <>
sudo ethtool eth4
=======================================================
sudo modprobe bonding
echo "bonding" | sudo tee -a /etc/modules

sudo nano /etc/netplan/01-bond.yaml

Attemping bonding  interfaces

network:
  version: 2
  renderer: networkd
  ethernets:
    eno1: {}
    eno2: {}
  bonds:
    bond0:
      interfaces:
        - eno1
        - eno2
      parameters:
        mode: active-backup
        primary: eno1
        mii-monitor-interval: 100
      dhcp4: false
      addresses: [192.168.1.50/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]


verify 

cat /proc/net/bonding/bond0
ip a show bond0




PowerStore Node A Port 0 ToR Data switch 2 port 1
PowerStore Node A Port 1 ToR Data switch 1 port 1
PowerStore Node B Port 0 ToR Data switch 1 port 2
PowerStore Node B Port 1 ToR Data switch 2 port 2




hostname S4128F-1

! Create VLANs
vlan 20
 name iSCSI
vlan 30
 name NFS

! Create vLT Peer-Link Port-Channel
interface port-channel 100
 description vLT Peer Link to S4128F-2
 switchport mode trunk
 mtu 9216
 no shutdown

! Add vLTi interfaces
interface ethernet 1/1/31
 description vLTi Link 1
 channel-group 100 mode active

interface ethernet 1/1/32
 description vLTi Link 2
 channel-group 100 mode active

! Configure vLT domain
vlt domain 1
 peer-link port-channel 100
 backup destination 10.0.0.2

! PowerStore Node A (vLT LAG)
interface port-channel 10
 description PowerStore Node A
 switchport mode trunk
 switchport trunk allowed vlan 20,30
 mtu 9216
 vlt-peer-lag 10

interface ethernet 1/1/1
 description PowerStore Node A eth1
 mtu 9216
 flowcontrol rx on tx on
 channel-group 10 mode active

! PowerStore Node B (vLT LAG)
interface port-channel 11
 description PowerStore Node B
 switchport mode trunk
 switchport trunk allowed vlan 20,30
 mtu 9216
 vlt-peer-lag 11

interface ethernet 1/1/2
 description PowerStore Node B eth1
 mtu 9216
 flowcontrol rx on tx on
 channel-group 11 mode active

! Uplink to Core (optional LACP)
interface port-channel 50
 description Uplink to Core Switch
 switchport mode trunk
 switchport trunk allowed vlan 20,30

interface ethernet 1/1/48
 description Uplink Link to Core
 channel-group 50 mode active



hostname S4128F-2

! Create VLANs
vlan 20
 name iSCSI
vlan 30
 name NFS

! Create vLT Peer-Link Port-Channel
interface port-channel 100show ip interface brief
 description vLT Peer Link to S4128F-1
 switchport mode trunk
 mtu 9216
 no shutdown

! Add vLTi interfaces
interface ethernet 1/1/31
 description vLTi Link 1
 channel-group 100 mode active

interface ethernet 1/1/32
 description vLTi Link 2
 channel-group 100 mode active

! Configure vLT domain
vlt domain 1
 peer-link port-channel 100
 backup destination 10.0.0.1

! PowerStore Node A (vLT LAG)
interface port-channel 10
 description PowerStore Node A
 switchport mode trunk
 switchport trunk allowed vlan 20,30
 mtu 9216
 vlt-peer-lag 10

interface ethernet 1/1/1
 description PowerStore Node A eth2
 mtu 9216
 flowcontrol rx on tx on
 channel-group 10 mode active

! PowerStore Node B (vLT LAG)
interface port-channel 11
 description PowerStore Node B
 switchport mode trunk
 switchport trunk allowed vlan 20,30
 mtu 9216
 vlt-peer-lag 11

interface ethernet 1/1/2
 description PowerStore Node B eth2
 mtu 9216
 flowcontrol rx on tx on
 channel-group 11 mode active

! Uplink to Core (optional LACP)
interface port-channel 50
 description Uplink to Core Switch
 switchport mode trunk
 switchport trunk allowed vlan 20,30

interface ethernet 1/1/48
 description Uplink Link to Core
 channel-group 50 mode active





ipaddrset -ip 192.168.10.10 -mask 255.255.255.0 -gw 192.168.10.1


show ip interface brief

sudo apt install multipath-tools
sudo multipathd show paths
sudo nano /etc/multipath.conf

sudo systemctl enable multipathd
sudo systemctl restart multipathd

---------------------------------------------------------------------------
defaults {
    user_friendly_names yes
    find_multipaths yes
}

blacklist {
    devnode "^sda"    # Exclude OS disk
}

devices {
    device {
        vendor "DELL"
        product "Unity|PowerStore"
        path_grouping_policy group_by_prio
        path_checker tur
        prio alua
        failback immediate
        hardware_handler "1 alua"
        no_path_retry 60
        rr_weight uniform
    }
}





---------------------------------------------------------------------------

sudo mkfs.ext4 -E discard /dev/mapper/<disk>

/dev/mapper/mpatha   =>  virtual disk

sudo mkdir /mnt/<mapping point>
sudo mount -o discard /dev/mapper/mpatha1 /mnt/<mounting point>


/etc/fstab
/dev/mapper/mpatha1 /mnt/powerstore-lun ext4 defaults,discard,_netdev 0 0


findmnt -o TARGET,FSTYPE,OPTIONS /mnt/powerstore-lun
 