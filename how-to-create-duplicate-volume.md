---

copyright:
  years: 2014, 2017
lastupdated: "2017-10-09"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# Creating a duplicate Block Volume

{{site.data.keyword.BluSoftlayer_full}} provides the ability to create a duplicate of an existing {{site.data.keyword.blockstoragefull}}. The duplicate volume inherits the capacity and performance options of the original LUN/volume by default and has a copy of the data up to the point-in-time of a snapshot.   

Because the duplicate is based on the data in a point-in-time snapshot, snapshot space is required on the original volume before you can create a duplicate.  To learn more about snapshots and how to order snapshot space, refer to [Snapshot documentation](snapshots.html).  

Duplicates can be created from both primary and replica volumes. The new duplicate is created in the same data center as the original volume.  For example, if you create a duplicate from a replica volume, the new volume will be created in the same data center as the replica volume.    

Duplicate volumes can be accessed by a host for read/write as soon as the storage is provisioned. However, snapshots and replication won't be allowed until the data copy from the original to the duplicate is complete. 

When the data copy is complete, the duplicate can be managed and used as a completely independent volume. 

This feature is only available for storage that is provisioned with encryption. Click [here](new-ibm-block-and-file-storage-location-and-features.html) for the list of available data centers. 

Some common uses for a duplicate volume:
- **Disaster Recovery Testing**: Create a duplicate of your replica volume to verify the data is intact and can be used if a disaster occurs, without interrupting the replication. 
- **Golden Copy**: Use a storage volume as golden copy that you can create multiple instances from for various uses. 
- **Data refresh**: Create a copy of your production data to mount to your non-production environment for testing. 
- **Restore from Snapshot**: Restore data on the original volume with specific files/date from a snapshot without overwriting the entire original volume with the snapshot restore function. 
- **Dev/Test**: Create up to four simultaneous duplicates of a volume at one time to create duplicate data for development and test. 
- **Storage Resize**: Create a volume with new size, IOPS rate or both without having to perform a host-based migration of your data.  
	

There are a couple of ways to create a duplicate volume through the [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}: 

## How to create a duplicate from a specific volume in the Storage List

Go to your list of {{site.data.keyword.blockstorageshort}}:

1. From the customer portal, click **Storage**, **{{site.data.keyword.blockstorageshort}}** OR from the {{site.data.keyword.BluSoftlayer_full}} catalog click **Infrastructure->Storage->{{site.data.keyword.blockstorageshort}}**. 
2. Select a LUN from the list and click **Actions** -> **Duplicate LUN (Volume)** 
3. Choose your snapshot option: 
    - If ordering from a non-replica volume:
      - Select **Create from new snapshot** – this will create a snapshot that will be used for the duplicate. Use this option if there are currently no snapshots for your volume or if you want to create a duplicate right then.
    
      OR 
      - Select **Create from latest snapshot** – this will create a duplicate from the latest snapshot that exists for this volume. 
    - If ordering from a replica volume – the only option for snapshot is to use the latest snapshot available. 
4. Storage Type (Endurance or Performance) and Location will remain the same as the original volume.
5. Hourly or Monthly Billing – you can choose to provision the duplicate LUN with hourly or monthly billing.  The billing type for the original volume is automatically selected. If you want to choose a different billing type for your duplicate storage, you can make that selection here. 
5. You can specify IOPS or IOPS Tier for the new volume if you want to. The IOPS designation of the original volume is set by default. Available Performance and size combinations will be displayed.
    - If your original volume is 0.25 IOPS Endurance tier, you won't be able to make a new selection. 
    - If your original volume is 2, 4, or 10 IOPR Endurance tier, you can move anywhere between those tiers for the new volume. 
6. You can update the size of the new volume so that it is larger than the original. The size of the original volume is set by default. 
    - **Note**: {{site.data.keyword.blockstorageshort}} can only be resized to 10 times the original size of the volume. 
7. You can update the snapshot space for the new volume to add more, less, or no snapshot space. The snapshot space of the original volume is set by default. 
8. Click **Continue** to place your order. 



## How to create aduplicate From a specific Snapshot

Navigate to your list of {{site.data.keyword.blockstorageshort}}:

1. Click on a **LUN/volume** from the list to view the details page. (It can either be a replica or non-replica volume.) 
2. Scroll down and select an existing snapshot from the list on the details page and click **Actions ->Duplicate**.   
3. Storage Type (Endurance or Performance) and Location will remain the same as the original volume. 
4. Available Performance and size combinations are displayed. The IOPs designation of the original volume is set by default. You can specify IOPS or IOPS Tier for the new volume. 
    - If your original volume is 0.25 IOPS Endurance tier, you won't be able to make a new selection. 
    - If your original volume is 2, 4, or 10 IOPS Endurance tier, you can move anywhere between those tiers for the new volume. 
5. You can update the size of the new volume so that it is larger than the original. The size of the original volume is set by default. 
    - **Note**: {{site.data.keyword.blockstorageshort}} can only be resized to 10 times the original size of the volume. 
6. You can update the snapshot space for the new volume to add more, less, or no snapshot space. The snapshot space of the original volume is set by default. 
7. Click **Continue** to place your order for the duplicate. 


## How to manage your duplicate volume

While data is being copied from the original volume to the duplicate, you'll see a status on the details page that states the duplication is in progress. During this time, you can attach to a host and read/write to the volume, but you can't create snapshot schedules. Once the duplication process is complete, the new volume will be completely independent and can be managed with snapshots and replication as normal. 
