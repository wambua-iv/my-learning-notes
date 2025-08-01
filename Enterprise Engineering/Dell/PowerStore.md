- Purpose-built All-Flash [Active/Active] storage appliance
    
    Active/Active is an architecture that is designed for high availability in which all components are active and available to perform a task if another component fails. Each node has access to the same storage resources.

Supports:

Non-Volatile Memory Express (NVMe) [Non-Volatile Memory Express (NVMe) is a communications protocol that is specifically developed for SSDs.] communications protocol
* NVMe Solid State Drive (SSD) for storage
* NVMe NVRAM devices for cache
* NVMe and SAS SSDs by expansion
* End-to-end NVMe_oF [Non-Volatile Memory Express over Fabric extends the NVMe protocol to connect hosts to storage appliances across a TCP/IP or Fiber Channel network fabric].(TCP and FC)

Dynamic Resiliency Engine (DRE) - Proprietary algorithms provide drive redundancy to protect against drive failure
Provides a more distributed, automated, and efficient way to protect data than traditional RAID

**PowerStore T**

Storage-centric—provides **Block-only** [Data is stored in separate blocks suited for applications requiring high performance and low latency.]**Unified** [Unified storage consists of block and file storage.]   
- Software stack starting with a CoreOS deployed directly on bare metal hardware.
- Uses NVMe, FC, and iSCSI for Block access.
- Uses NFS and SMB for File access.   
	- Up to four appliances can be connected in a cluster.
	- Different PowerStore T models are supported in the same cluster.
	- All appliances within a PowerStore T cluster must be on the same **PowerStoreOS version.**

**PowerStore X**

**Block-only** storage with an integrated Hypervisor (ESXi) installed on the bare metal.
- Controller Virtual Machine (VM) with CoreOS is deployed on the ESXi hypervisor.
- With VMware license, customer can deploy additional VMs (Customer VMs) and install custom applications.
- Uses NVMe, FC, and iSCSI for Block access.
	- - Up to four appliances can be connected in a cluster.
	- - Different PowerStore X models are supported in the same cluster.
	- - All appliances within a PowerStore X cluster must be on the same PowerStoreOS version.
>[!info]
> The mixing of PowerStore T with PowerStore X in a cluster is not supported


**Scaling up is adding more Expansion Enclosures.**

- Increases storage capacity.
- Up to three expansion enclosures per appliance

**Scaling out is adding more Base Enclosures**.
- Increases processing power and storage.
- Up to four appliances per cluster.

Model 500 only offered as a PowerStore T.
Models 1000, 1200, 3000, 3200, 5000, 5200, 7000, 9000, and 9200 are available as PowerStore T and X.

    There is no 7200T or X.

- Drives 0–20: NVMe SSD or SCM Drives
    
- Drives 21–24: NVMe NVRAM SSDs (except PowerStore 500)
    For systems that use two NVMe NVRAM drives, slots 21 and 22 must remain unpopulated
 PowerStore 500 has ***six*** 288-pin sockets with **16 GB DDR4 DIMMs.** On the PowerStore 1000 to 9200 models, each node has ***24*** 288-pin DIMM sockets

On **PowerStore 1000 to 9200** models, the CPU board on each node has slots for **two internal M.2 modules**. One internal M.2 boot module is used for **general system operations**, and the other internal M.2 boot module is used for **recovery**.

On the **PowerStore 500** model, each node has **one 240 GB M.2 boot module**. The PowerStore 500 M.2 module is used for **cache** to recover from a system failure.

A node can run with a single fan failure
PowerStore 500 => 6 fans others have 7

- **SAS SSDs** can **only** be added to **ESS25 SAS Expansion Enclosures.**
- A minimum of six data drives must be used.

During a power failure, the DC BBU supports cache vaulting to the internal M.2 SSD. It works differently than the BBU and NVRAM self-vaulting process that PowerStore 1000-9200 uses

___Systems having only NVMe SCM drives do not support expansion enclosures___
- In mixed SSD/SCM combinations, the SCM drives are used for metadata tiering (done automatically by the system)
    - In mixed SSD/SCM combinations, SCM drives do not add to capacity for user data

PowerStore 500

A 4-port card (MEZZ 0) is optional and only required for unified deployment (file services) and clustering
		Only the 4-port 25 GBE SFP-based Mezz card is supported.
		- The card supports 1 GbE SFP to RJ45, 10 GbE SFP, 25 GbE SFP28.
			- 25 GbE passive TwinAx.
			- 10 GbE active or passive TwinAx.

## I/O Module Types

**4-Port 25 GbE SFP-based** I/O module is supported in PowerStore T models.
	- 25 GbE SFP to RJ45
	- 25 GbE SFP-based SFP passive TwinAx
	- 10 GbE or 25 GbE SFP28
	- 10 GbE active or passive TwinAx
	- 1 GbE SFP to RJ45

The 4-Port 32 Gb Fibre Channel I/O module is supported in PowerStore T and PowerStore X models. The I/O module is used to serve Fibre Channel block protocol using SAN to hosts. Each port has an optical 16 Gb/32 Gb

- PowerStore supports NVMe/TCP over all front-end Ethernet ports.
	- vSphere 7.0U3
	- Red Hat Enterprise Linux 8.2-8.4
	- SLES 15 SP2-SP3
- All Ethernet modules support block access over NVMe and iSCSI simultaneously.

Front Indication Panel
	- Solid blue – Expansion enclosure is powered on and healthy
	- Blinking blue – System ID mode for identification of the enclosure in the rack
	- Blinking Amber – Hardware fault

Slot 0 has a 16
lane PCIe channel, and Slot 1 has an eight lane PCIe channel. The 4-Port 32 Gb Fibre Channel I/O module or the 4-Port 25 GbE SFP-based I/O module should be installed in Slot 0 when possible. The 100 GB Ethernet I/O module must be installed in Slot 0

=>* ==NVMe Supported Configurations==
= A cluster may have both SAS and NVMe expansion enclosures, but not attached to the same appliance.
 = ENS24 expansion enclosure
 Requires NVMe connectivity from new embedded module v2, 100 GbE 2-Port card, or 4-port 25 GbE card for the PowerStore 500

Drives may be added to the expansion enclosure one at a time or
multiple drives simultaneously.
Drives may move between base and ENS24 Expansion Enclosure
Drives may move between different ENS24 expansion enclosures within the same appliance. Supported drives on ENS24 Expansion Enclosure:
	NVMe SSD - PCIe, 1.92 TB – 15.36 TB (Raw capacity base-10)
Supported drives on base enclosure:
	NVMe SSD - PCIe, 1.92 TB-15.36 TB (Raw capacity base-10)
	NVMe SCM SSD - PCIe, 750 GB (Raw capacity base-10)

==Top of Rack Switch==

PowerStore T uses two data switches and a management switch.
PowerStore X uses two data switches. Management runs through the data switch connections.[PowerStore X does not support OOB management.]
Switch ports must support untagged native VLAN traffic for system discovery



--------------------------------------------------------------
PowerStore manager
----------------------------------------------------------------------------------

PowerStore X

PowerStore X requires all networks to be unique. It is highly recommended to deploy PowerStore X with multiple unique VLANs to separate the traffic or multiple unique subnets
Does not support NAS services

_Ports 0 and 1_ on the PowerStore X model appliance nodes are reserved for the Cluster network.

Management network provides access to:

- Infrastructure services, such as DNS, NTP, and SMPT
- PowerStore REST API, PowerStore Manager, and PowerStore CLI
- Support Connectivity
- vCenter
	-__A minimum of ==five__ IPs per PowerStore X== appliance is required per VLAN or subnet for Management.

When a PowerStore node boots, it assigns itself an IP address on IDN.

- The address is within the 169.254.0.0/16 IPv4 network
- The IDN uses the native VLAN only.
	IDN network uses the same cabling as the Management Network.
	On PowerStore X systems, IDN uses the first two ports of the 4-port card on each node.

- The Intra-Cluster Data (ICD) network[Internal storage network that provides continuous storage connectivity between nodes and appliances in the PowerStore cluster. For example, NAS servers use the ICD network to access underlying storage resources.]
- Intra-Cluster Management (ICM) networks[Internal management network that provides continuous management connectivity between nodes and appliances in the PowerStore cluster. For example, during a NAS installation, the PowerStore nodes communicate with each other using the ICM network.]both use the PowerStore backplane.
    
    - The benefit of moving the ICD to the backplane is that installs and upgrades can be completed without potential network misconfigurations in ToR switches.

For each PowerStore X appliance:

- At least **six** IP addresses are required. For best performance, it is recommended to have a minimum of **eight** IP addresses.
- To create a Global Storage Discovery IP Address, a minimum of **seven** IP addresses, and a recommended **nine** IP addresses are required for each appliance.

NVMe/TCP Storage Network
	Provides front-end connectivity for block storage access.
	Is configured after initialization in PowerStore Manager to support block storage access operations.
	Can be cabled through any port on the 4-port card or I/O module.

Replication and Block Import is a shared network. The Block Import network is not used for any File functionality. Only the Replication portion of the network, with File Mobility, is used for replication of File storage.
vMotion requires 2 IP addresses
None of the ports on PowerStore X form an LACP bond.

VLTi is required for Link Aggregation Control Protocol (LACP) connectivity to the core uplink switches, and:
    Allows a single device to use a LAG across two upstream switches.
    Assures high availability.
    Provides a loop-free topology and eliminates STP-blocked ports.
    Optimizes the use of all available uplink bandwidth.
    Guarantees fast convergence if either a link or a device fails.
    Provides Layer 2 multipathing.
    Provides link-level resiliency.

If neither MC-LAG nor reliable L2 uplinks are viable options, use a direct trunk link between the switches.
PowerStore requirements for this type of connectivity include:
- No SPOF. Two or more cable connections joined by Dynamic LAG.
- Dynamic LAG link allows traffic for the native VLAN and PowerStore network VLANs.
- The combined bandwidth of the link between the Physical Ethernet switches must be at least the recommended minimum bandwidth (RMB).
- Spanning tree protocol must be enabled on the Physical Ethernet switches with the link ports marked as STP non-edge ports.

![[connectivitytypes.png]]

__Network Setup Preparation Worksheet__ is completed with the help of a network administrator. It includes ports, priorities, and VLANs to use when setting up the network.
__Initial Configuration Worksheet__ includes all of the information required to complete the Initial Configuration Wizard (ICW), including the cluster name, appliance service tags, and IP addresses to use when completing the PowerStore X configuration.

Use these connection settings:
- Baud rate: **115200** (9600 for micro-USB port)
- Data bits: **8**
- Parity: **None**
- Stop bits: **1**
- Flow Control: **None**

## TOR configuration

*hostname* => `configure terminal`
			`hostname _hostname_`
*version* => `show version`
*license* => `show license status`
*management port* => (interface mgmt chassis/slot/port)
	#(config) interface mgmt 1/1/1
	#(config-if-ma-1/1/1) no shutdown
	#(config-if-ma-1/1/1) no ip address dhcp
	#(config-if-ma-1/1/1) ip address 172.17.57.20/24
	#(config-if-ma-1/1/1) exit
 => `management route <IP/netmask gateway>` to define the default gateway manage the system
 =>  ntp server [IP] to set the NTP server IP address.

##### VLAN creation

	#(config) interface vlan  ID 1502
	#(conf-if-vl-1502) description Storage_Network
	#(conf-if-vl-1502) no shutdown
	#(conf-if-vl-1502) exit

[Configure VLT]


### Service Port Access
For the **PowerStore Manager (UI)**, use https://128.221.1.250 or 251. UI access requires management services, which only run on the primary node on PowerStoreOS Version 1 and 2, and on the [non-primary Node] on PowerStoreOS version 3 and higher. If node A's IP does not work, try node B's IP address. The UI (PowerStore Manager) may not be accessible if management services or control path are not running or are experiencing issues.


=>Licenses are permanent **no expiration**

CLI Supported tasks include:
	- Configuring and monitoring the system
	- Managing users
	- Provisioning storage
	- Protecting data
	- Controlling host access to storage



### PowerStore T

Management Network => can be on the native VLAN
PowerStore node boots, it assigns itself an IP address 169.254.0.0/16
{Initial Discovery Network} runs over the native VLAN only = uses  dedicated management port
File Mobility is a prerequisite for replication and import of File storage
	- PowerStore T model must have been deployed in Unified mode
	- Three IP addresses must be reserved for File Mobility

File Import network is used with the File Mobility network to import File
storage from remote systems

Ports 0 and 1 on the PowerStore T model appliance node are  reserved for the Cluster network.

Replication and Block Import is a shared network. The Block Import network is not used for any File functionality. Only the Replication portion of the network, with File Mobility, is used for replication of File storage

[One out-of-band (OOB) management switch and two Top-of-Rack (ToR)] switches are required for PowerStore T to support iSCSI or NVMe/TCP host connectivity.

A maximum of 32 storage networks can be configured. Up to eight storage
networks can be configured per PowerStore interface.

`spanning-tree mode rstp` to set the switch mode to Rapid Spanning Tree Protocol (RSTP).



## Powerstore X
Supports block (SAN-only), and vVol workloads with a hypervisor installed on the system
Does not support NAS services


deployed with multiple VLANS 
Ports 0 and 1 on the PowerStore X model appliance nodes are reserved for the Cluster network.
Management, Storage, vMotion => configured in the first time cluster is created through initial configuration wizard

The Cluster network is used for intracluster management and data
communications.
- The cluster network is encrypted with IPSEC



[Replication and Block Import] => configured after powerstore manager initialization
	Uses front-end traffic for block storage and external data mobility traffic
	shares ports with iSCSI network => sepatation is using VLANS
		Shared port: Port 2 on I/O Module 0

[NVMe TCP]
	uses dedicated port => Port 0 on I/O Module 0	

[vMotion]
 = Configured during system initialization
 = Two IPs are required for each PowerStore X appliance.
 = 


Configure NVMe/TCP on supported host types:
	VMware vSphere 7.0U3 (supported with SmartFabric Storage Software).
	Red Hat Enterprise Linux (RHEL) 8.2-8.4
	SUSE Linux Enterprise Server (SLES) 15 SP2-SP3


**double drive** failure is only available if the appliance has seven or more data drives, excluding the NVMe NVRAM drives.



Only the **[Security Administrator]** and **[Administrator]** can 
	manage users in the system.
	can lock and unlock users.

![[Pasted image 20250723115140.png]]


Port: Upload AD or LDAP Server CA trust certificate for LDAP Secure
	Default ports: [389] for LDAP, [636] for LDAPS, [3268] for LDAP Global Catalog, and [3269] for LDAPs Global Catalog

Volumes are mapped during creation or after creation
	- Host must have an initiator that is registered on powerstore by FC, ISCSI IQN or NVMe initiator NQN 

= iSCSI initiators are automatically discovered by PowerStore once they are connected to at least one target [CHAP authentication must be disabled in the cluster for the autodetection of iSCSI initiators to work.]

#### [Fibre Channel and NVMe initiators]
= appliance has at least one Fibre Channel I/O Module on each node
= one Fibre Channel port from the host and at least one Fibre Channel port from the appliance are connected to the same switch fabric 
= PowerStore Fibre Channel ports are configured on the same Fibre Channel switch zone


A host can belong to one host group only
If the host has mapped volumes => it cannot be added to the host group



**[Configuring MPIO]**

PowerStore supported CHAP configurations:
[Single (initiator enabled)]
[Mutual (initiator and target)]



volume is a single unit that represents a specific quantity of block storage
Volume groups are logical containers for a group of volumes. A volume can only be a member of one volume group
[block access services => NVMe, FC and iSCSI]
Add up to [100 volumes] with the same properties at a time
PowerStore supports increasing the volume size, but not decreasing it.
PowerStore series supports the creation of thin volumes up to 256 TB

[protection policy] applies to all members of the volume group. The individual volumes within the group cannot not have separate policies

**Apply [write-order] consistency to protect all volume group members**
	=> a single replication session is created for the entire volume group,
	=> volume group write-order-consistent ensures that the write order is preserved among members when snapshots of the volume group are taken

Volume renaming
	=> requires pausing the replication

Application tags cannot be assigned to volumes groups.


iscsiadm -m session --rescan

removing a volume from a volume group
	=> If the volume group has a replication rule, the volume that is removed retains the policy
	=> volume is not compliant until the volume group is synchronized to the destination
	=> volume group is synchronized to the destination, the removed volume is also removed from the destination volume group

Removing a volume from a volume group that is replicating, causes an error message stating that it cannot create a replication session for the volume => reapply the protection policy.
[removed volume cannot be deleted] until all volume group snapshots (taken when the volume was a part of the group) expire or are deleted


removed volume has existing snapshots, those volume group snapshots cannot be used to refresh or restore the removed volume.
	=> Restore and refresh operations require that the volume group membership match the membership that existed when the snapshot was taken.

PowerStore X models, volumes can only be provisioned to the internal ESXi hosts using PowerStore REST API or CLI.

#### [internal migration]
moves volumes or volume groups from one appliance to another within the cluster
migrating a volume or volume group, all associated snapshots and thin clones migrate with the storage resource.
Migration [always requires user action].
	=> Manual migration
	=> Assisted migration
			system periodically monitors storage resource utilization across the appliances. Migration recommendations are based on factors such as drive wear, appliance capacity, and health. If a migration recommendation is accepted, a migration session is automatically created
			[Before beginning the migration, a prompt to acknowledge rescan appears]
	Migration states
			synchronizing => background data coping
			cutover phase => [final phase] ownership of the volume or volume group is transferred to the new appliance
		[Migration is asynchronous] until the cutover occurs and can be paused or cancelled [delete and cancel buttons]anytime during the migration. Before cutover all volumes are fully synchronized.

At volume creation volume placement is automatic
	-> there is an option to specify volume placement


#### [Local Data protection]
Protection policies are configuration profiles that combine data protection rules
=> only one protection policy per storage resource[resources => volumes, volume groups, thin clones, virtual machines, file systems].
=> Snapshot [read-only point-in-time] and replication rules.
=> protection policy can include up to four snapshot rules, and no more than one replication rule

Remove a protection policy:
 = Before moving the volume to a protected volume group, or
 = Before deleting a volume.

Replication parameters
	A replication destination
	The Recovery Point Object (RPO) => data replication frequency
	An alert threshold for the RPO.

Volume snapshots [are read-only]. You cannot add to, delete from, or
change the contents of a Volume snapshot. [Snapshots cannot be refreshed]

File system snapshots can be refreshed.[ updating contents of a file system snapshot]
Hosts or NAS clients have no direct access to a snapshot.[create clones of the snapshot]
	 volume snapshot clone: Map to a host.
	 file system snapshot clone: Create an NFS export or SMB share.

#### Snapshot restore
= resets the data in the parent storage resource to the point in time at which the snapshot was taken. [for a  volume group all members of the group]
		-Disconnect hosts from accessing the volume during restore operations

#### [Thin Clone]
= read/write copy of the storage resource that shares blocks with the parent resource
=> can establish hierarchical snapshots to preserve data over different stages of data changes within a [Base Volume Family].

![[Pasted image 20250729124445.png]]

Data efficiency

=> zero detection
	=> zero Detection detects zeros and discards them
	=> Deduplication => deduplication works at the block level at 4KB granularity.
	=> compression => uses physical hardware to encode data using fewer bits
than the original representation


Data Encryption
	=> Data At Rest Encryption
		protects from 
			Stolen Drive
			 During transit
			 Discarded Drive
	Self-Encrypting Drives using [Advanced Encryption Standard](AES 256-bit) 
		

[Host Group rules]
	A host can belong to one host group only.
	If the host has mapped volumes, it cannot be added to the host group.
	A host cannot be added to a host group that uses a different protocol.


![[Pasted image 20250730103857.png]]

Each NAS server is a separate virtual file server.
Each NAS Server allows network access to PowerStore-hosted files and folders via network file sharing protocols.
Each NAS Server can be associated with one or more file systems.
NAS supports the follwing protocols: [SMB, NFSv3, NFSv4, FTP, and SFTP].
Access can be provided to UNIX or Linux and Windows clients simultaneously.
NAS Servers can be secured and isolated logically at the file system, network, and authentication level.
VLAN separation in NAS server context supports network multitenancy.


[file system]
Up to 256 TB usable capacity per file system, minimum size of a file system is 3GB, 1.5GB is set aside for metadata
Extend and shrink capabilities
Capacity consumption that is limited by user and tree quotas

[NAS server]
Each NAS node runs in a NAS docker container.
NAS Network Heartbeat[fixed intervals of 1 sec] enables data service high availability (HA), which results in NAS server failover when a threshold is reached

NAS HA:
	 Fault tolerance
	 Load balance at failover
	 Restarting all NAS nodes

heartbeat failed for more than [five seconds], the node is considered as failed and high availability (HA) is triggered


NAS [fencing] => Fencing is the isolation of a failed node so that it does not cause disruption to a cluster.

Operating on every node, the NAS server failover logic:
1. Detects the faulty NAS node.
2. Fences the faulty NAS node.
3. Moves the NAS servers to the backup NAS node.



[types of file systems]
General => Appropriate General use with NFS/SMB
VMware File Systems => optimized for VMware workload. Available to NFS datastore
	Does not support Quotas
	Does not support [File level retention] => {*preventing modification of file system data* [Enterprise (FLR-E)], an authorized **storage administrator** can delete an FLR-E file system 
	[Compliance (FLR-C)] => **Storage administrators/Dell Support** cannot delete files }

FLR-C enabled file systems are compliant the Securities and Exchange Commission (SEC) rule
FLR-C does not support snapshot restoration.


Sync Writes Enabled
	Setting is required when using SMB shares to store and access database files.
	system performs immediate synchronous writes and reduces the chances of data loss

[Oplocks Enabled]
	Allows SMB clients to buffer file data locally before sending to the system. SMB clients can then work with files locally and periodically communicate changes

![[Pasted image 20250731120500.png]]

PowerStore T model supports NFSv3 and NFSv4

SMB shares have a default value of 022


To enable NFS file sharing, configure a NAS server with:
	Naming services
	NFS sharing protocol