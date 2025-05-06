
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