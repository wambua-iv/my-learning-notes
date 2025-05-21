
Scale up => add disk capacity
Scale Out =>  Addinng storage processors
Backend PCIe => used internally in the system

On high-end models, each controller has four CPUs, or
vNodes. On mid-range models, each controller has two vNodes. 

>[!tip]
>IP scale out => entry level and mid range converged storage {v3/ v5} X10 Kunpeng series, Dorado series and new gen hybrid storage series
>
>[PCIe Scale out] => converged storage 18000 V3,V5 and Dorado V3 series


SmartCache => makes use of SCM drives to compose a SmartCache pool
	caches frequently accessed data shortening response time
Smart Cache partition => partition is a logical concept based on SmartCache pool


SmartCache => makes use of SCM drives to compose a SmartCache pool
	caches frequently accessed data shortening response time
Smart Cache partition => parttion is a logical concept based on SmartCache pool

Stores LUNs and file Systems

__Smart Acceleration__ 
	SSD  in the performance tier are shared by multiple disk domains
	Performance layer is distributed by the system to each pool
	performance layer converges tiers and caches

SmartQOS 
	supports the addition of one type of object per policy



![[deduplication.png]]

![[compression.png]]


Snapshot => point in time copy of data
	- Achieved through: 
		- copy-on-write = data to be copied in the initial data write process
			- If a block in the original volume is modified after the snapshot:
			- The system **first copies** the original (unchanged) block to a **snapshot reserve**.
			- Then the write proceeds to update the block in the main volume.
		- redirect-on-write =
			- when a write occurs after the snapshot is taken, the new data is written to a new location, and the original block is left untouched for the snapshot to reference.

|Feature|Copy-on-Write (CoW)|Redirect-on-Write (RoW)|
|---|---|---|
|Write Handling|Copies original block before write|Writes new data elsewhere|
|Snapshot Integrity|Maintained by preserving original|Maintained by not modifying original|
|Write Performance|Slightly degraded|Often faster|
|Fragmentation Risk|Low|Higher|
|Read Complexity|May involve multiple reads|Simpler (from active volume)|

Cross-Level Rollback refers to restoring a system (volume, VM, or file system) not to the most recent snapshot, but to a specific earlier point in a snapshot chain or hierarchy


DTOE nic => Datacenter TCP Offload Engine

NVMe over Fabrics can map NVMe commands and data to multiple
fabric links, including Fibre Channel, InfiniBand, RoCE v2, iWARP, and TCP.
The Huawei all-flash storage system using the end-to-end NVMe design
reduces the minimum access latency to less than **100 μs.**

LUNs deployed on Multiple controllers to provide high performance and high reliability
	Huawei makes use of a distributed Active/Active architecture.


Zero RTO/RPO using SmartMatrix
OceanStor Dorado innovates and improves the overall software
architecture to implement a truly distributed active-active architecture, end-to-
end NVMe, and global resource sharing.

[Global caching]: Each LUN is divided into slices and then evenly distributed
to all of the controllers with the distributed hash table, or DHT algorithm
for parallel processing
		- Global Cache Pre-fetching
		- Read hits


### Converged NAS and SAN
SAN and NAS Protocols are isolated

![[SAN-NAS.png]]


[SmartMatrix Full-Mesh Hardware Architecture]

[E2E full-mesh]
	- FE interconnection  and Sharing among the Four controllers
		- I/O modules are fully interconnected with all  controllers in the enclosure
	- Fully interconnected Controllers
	- Back-end interconnection and sharing among eight controllers. [Back-end	interconnect I/O modules are used for expansion]. Each module connects to the four controllers in a controller enclosure and can be accessed by the	four controllers simultaneously

[Active-Active High Availability Architecture]
![[Active-Active.png]]

Scale Out file system architecture
[granularity]
	directories are distributed to multiple CPUs for processing, and files in directories are distributed to the same controller node to ensure that directories and files are processed by the same CPU, achieving optimal file read/write performance and directory processing performance.
[Distribution]
	Logical partitions [file service partitions] (FSPs) 
		- are designed on vNodes to carry directories and files of file systems. 
		 - are evenly distributed on vNodes of all controllers. When a storage 
		 - system expansion from dual controllers to more controllers, FSPs are automatically and evenly distributed to new controllers. [In this way, the number of FSPs remains unchanged regardless of the number of vNodes], achieving balanced processing of files and directories among multiple controllers and CPUs.
		Distribution Modes
			- balanced mode
			- performance mode
Raid TP:
	=> supports Triple Disk failure
	=> Effiecient Disk Reconstruction

Protection Information
	- 8 bytes of Data is written to disk for every 512 bytes of data


• Default roles of system administrators:
	▫ Super administrator (super_administrator)
	▫ Administrator (administrator)
	▫ Security administrator (security_administrator)
	▫ SAN administrator (san_administrator)
	▫ NAS administrator (nas_administrator)
	▫ Data protection administrator (dataProtection_administrator)
	▫ Network administrator (network_administrator)
• Default roles of vStore administrators:
	▫ vStore administrator (vStore_administrator)
	▫ vStore data protection administrator (vStore_dataProtection)
	▫ vStore protocol administrator (vStore_protocol)


[SmartMulti-Tenant] - divides one physical storage system into multiple virtual storage systems to provide different services for different clients and improve storage utilization
	- logical resources are associated with vStores for isolation and security purposes

Each vStore has independent protocol instances, including the SMB, NFS, iSCSI, and Fibre Channel services.

[SmartDedupe] - is a data deduplication technology
Types of deduplication:
	- [Inline deduplication]: Data is deduplicated when being written to storage media.
	- [Post-process deduplication]: Data is first written to persistent storage media and then read for deduplication.
	- [Fixed-length deduplication]: Data is divided into blocks of a fixed size for deduplication.
	- [Variable-length deduplication]: Data is divided into blocks of variable sizes based on its content for deduplication. This is mainly used in data backup.
	- [Similarity-based deduplication]: The system divides data into blocks of a fixed size and analyzes the similarity among the blocks. The system deduplicates the identical data blocks and performs combined or delta compression on the similar data blocks.


[SmartCompression] is a data compression technology
Types of compression:
	- [Inline compression]: Data is compressed when being written to storage media.
	- [Post-process compression]: Data is first written to storage media and then read for compression.
	- [Software compression]: Uses the CPU of a system to perform a compression algorithm.
	- [Hardware compression]: The compression algorithm logic is fixed to the hardware device, for example, the acceleration engine, FPGA, or ASIC of the Arm CPU. The compression API provided by the hardware device is invoked when the compression algorithm logic is used.
	- [Lossy compression]: A compression mode in which original data cannot be completely restored after decompression. This mode is commonly used to process audio, video, and images.
	- [Lossless compression]: A compression mode in which original data can be completely restored after decompression.



[HyperReplication] is a core technology for remote DR and backup
	Synchronous Remote Replication
		- data is synchronized in real time to achieve full protection for data consistency, minimizing data loss in the event of a disaster.
	Asynchronous Remote Replication
		- data is synchronized periodically to minimize service performance deterioration caused by the latency of long-distance data transmission.

initial synchronization is complete, the data status of the secondary
LUN becomes Consistent

Running status of  Replication Pair
Normal => Data synchronization between the primary and secondary LUNs is complete.
Split => Data replication between the primary and secondary LUNs is suspended. The running status of a pair changes to Split after the primary and secondary LUNs are manually split.
Interrupted =>  the pair between the primary and secondary LUNs is interrupted
To be recovered => remote replication pair requires restoration using a manual policy after the fault that caused a pair interruption is rectified
Invalid => original properties of a primary or secondary LUN change while a pair is interrupted
Synchronizing => When data is being synchronized from the primary LUN to the secondary LUN, the secondary LUN cannot be read or written. If a disaster occurs at this time, data on the secondary LUN cannot be used for service recovery. 

after a remote replication pair is configured, for the host, the [secondary LUN is read-only by default.] -> write protection must be removed to make the secondary LUN able to take over the roles of the primary LUN

[HyperDetect] is a data protection technology deployed in storage system containers. It provides _ransomware detection capabilities_, including __ransomware file interception__, __real-time ransomware detection__, and __intelligent ransomware detection__

[OceanProtect] => 
	- OceanProtect separates backup software metadata and backup data based on the data layout through feature-based processing

### => Scale Out Technologies
OceanStor Pacific series is an intelligent scale-out storage product that provides block, file, object, and HDFS services.

scale-out storage system supports node-level security and cabinet-level security

[Storage interface layer]: provides standard interfaces for applications to access the storage system
[Storage service layer]: provides file, object, HDFS, and block services and advanced features [hyperreplication, HyperMetro, SmartQuota, SmartQOS]
[Storage engine layer]: implements persistent storage

[Elastic Erasure Encoding]
	- N data fragments (N is an even number) and calculates M parity fragments (M can be 2, 3, or 4)
	- if the number of normal nodes is less than N + M, the system reduces the redundancy ratio for newly written data to (N – 1)+M (the ratio can be reduced to N/2+M at a minimum) before the fault is rectified
		- If the number of storage nodes is greater than or equal to N+M, the N+M redundancy ratio is used. In this case, the system can tolerate simultaneous failures of M disks or M nodes. If the number of storage nodes is greater than or equal to (N + M)/M and less than N + M, the N+M:1 (folded) redundancy ratio is used. In this case, the system can  tolerate simultaneous failures of M disks or one node.


![[Pasted image 20250519172940.png]]


[HyperMetro]:  => active-active storage pair.

[Software Integrity Protection]
[Data deduplication]
[File Replication]
[SAN: High-Performance Optimistic Lock]
[Hypermetro]
[Data Protection]
[Redirect on Write]


Cross-Level Rollback refers to restoring a system (volume, VM, or file system) not to the most recent snapshot, but to a specific earlier point in a snapshot chain or hierarchy

|Feature|Description|
|---|---|
|**Parallel Architecture**|Uses **multiple CPU cores/threads** to deduplicate data blocks simultaneously.|
|**Inline and Post-processing**|Supports both **real-time (inline)** deduplication and **scheduled (post-process)** modes.|
|**Global Deduplication**|Operates across entire storage pools, not just per volume or LUN.|
|**Metadata Optimization**|Maintains deduplication indexes in-memory and on SSDs for fast access.|
|**Multi-node Scaling**|In distributed systems (e.g., OceanStor), deduplication scales across nodes.|
|**Integration with Compression**|Often used with compression to improve overall space savings.|