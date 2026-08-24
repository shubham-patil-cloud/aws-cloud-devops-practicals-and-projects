# AWS EBS Practical

This project demonstrates practical operations with **Amazon Elastic Block Store (Amazon EBS)**. It includes creating and attaching EBS volumes, partitioning and formatting disks, mounting volumes, creating snapshots, copying snapshots between AWS Regions, and restoring volumes from snapshots.

## 📌 Technologies Used

* AWS EC2
* Amazon EBS
* Linux
* Amazon Linux
* SSH
* `lsblk`
* `blkid`
* `fdisk`
* `mkfs`
* `/etc/fstab`

## 📚 Topics Covered

* Checking storage devices on EC2
* Creating an EBS volume
* Attaching an EBS volume
* Creating disk partitions
* Formatting partitions with XFS
* Mounting and unmounting volumes
* Configuring automatic mounts
* Creating EBS snapshots
* Copying snapshots between Regions
* Creating volumes from snapshots
* Attaching restored volumes
* Deleting volumes and snapshots

---

## 1. Check Storage Devices

Connect to the EC2 instance using SSH and check the available block devices.

```bash
lsblk
```

This command lists:

* Disks
* Partitions
* Attached block devices

To display UUIDs, filesystem types, and device information:

```bash
sudo blkid
```

You can also check the automatic mount configuration:

```bash
cat /etc/fstab
```

To view devices under the Linux device directory:

```bash
cd /dev
ls
```

---

## 2. Create an EBS Volume

1. Open the **AWS Management Console**
2. Go to **EC2**
3. Select **Elastic Block Store → Volumes**
4. Click **Create volume**
5. Select the required configuration:

```text
Volume type: gp3
Size: 5 GiB
IOPS: Default
Throughput: Default
Availability Zone: Same as the EC2 instance
```

6. Click **Create volume**

> The EBS volume must be created in the same Availability Zone as the EC2 instance that will use it.

---

## 3. Attach the EBS Volume

1. Go to:

```text
EC2 → EBS → Volumes
```

2. Select the created volume
3. Click **Actions → Attach volume**
4. Select the EC2 instance
5. Select the recommended device name
6. Attach the volume

Check the attachment status from the AWS Console.

Then connect to the EC2 instance using SSH:

```bash
ssh -i <key-file.pem> ec2-user@<public-ip>
```

Check the attached disk:

```bash
lsblk
```

---

## 4. Create Disk Partitions

Identify the new EBS device. It may appear differently depending on the EC2 instance type.

Open the disk partition tool:

```bash
sudo fdisk /dev/nvme1n1
```

Inside `fdisk`, create partitions.

Useful commands:

```text
n = Create a new partition
p = Display the partition table
d = Delete a partition
w = Write changes and exit
q = Quit without saving
```

Example: create three partitions of approximately `1 GiB` each.

After creating the partitions, verify them:

```bash
lsblk
```

The partitions may appear similar to:

```text
nvme1n1
├── nvme1n1p1
├── nvme1n1p2
└── nvme1n1p3
```

---

## 5. Create XFS Filesystems

Check the available devices:

```bash
lsblk
```

Check UUID and filesystem information:

```bash
sudo blkid
```

Create XFS filesystems:

```bash
sudo mkfs.xfs /dev/nvme1n1p1
sudo mkfs.xfs /dev/nvme1n1p2
sudo mkfs.xfs /dev/nvme1n1p3
```

> Never run `mkfs` on a partition that contains data you need. Formatting destroys the existing filesystem data.

Verify the filesystem:

```bash
sudo blkid
```

---

## 6. Mount the Partitions

Create mount directories:

```bash
sudo mkdir /opt
sudo mkdir /mnt
```

Mount the partitions:

```bash
sudo mount /dev/nvme1n1p1 /opt
sudo mount /dev/nvme1n1p2 /mnt
```

Verify the mounted filesystems:

```bash
lsblk
```

You can also check disk usage:

```bash
df -h
```

---

## 7. Configure Automatic Mounting

Edit the `/etc/fstab` file:

```bash
sudo vim /etc/fstab
```

Add the required mount entries using the UUIDs of your partitions.

Example:

```text
UUID=<partition-1-uuid> /opt xfs defaults,nofail 0 2
UUID=<partition-2-uuid> /mnt xfs defaults,nofail 0 2
```

Check the configuration:

```bash
cat /etc/fstab
```

Test the configuration:

```bash
sudo mount -a
```

Verify:

```bash
lsblk
df -h
```

---

## 8. Unmount the Partitions

To unmount the filesystems:

```bash
sudo umount /opt
sudo umount /mnt
```

Verify that they are no longer mounted:

```bash
lsblk
```

---

# 🌍 EBS Snapshot and Cross-Region Transfer

## 9. Create an EBS Snapshot

1. Switch to the source AWS Region, for example:

```text
US East (N. Virginia)
```

2. Go to:

```text
EC2 → EBS → Volumes
```

3. Select the required volume
4. Click:

```text
Actions → Create snapshot
```

Wait until the snapshot is completed.

---

## 10. Copy a Snapshot to Another Region

To copy the snapshot to another AWS Region:

1. Go to:

```text
EC2 → EBS → Snapshots
```

2. Select the snapshot
3. Click:

```text
Actions → Copy snapshot
```

4. Select the destination Region, for example:

```text
Asia Pacific (Mumbai)
ap-south-1
```

5. Click **Copy snapshot**

AWS creates a copy of the snapshot in the destination Region.

---

## 11. Create an EBS Volume from the Copied Snapshot

Switch to the destination Region:

```text
Asia Pacific (Mumbai)
```

Then:

1. Go to:

```text
EC2 → EBS → Snapshots
```

2. Select the copied snapshot
3. Click:

```text
Actions → Create volume from snapshot
```

4. Select an Availability Zone that matches the EC2 instance
5. Create the volume

### Result

```text
Original EBS Volume
        ↓
    Snapshot
        ↓
   Copy Snapshot
        ↓
Destination Region
        ↓
Create New Volume
```

---

## 12. Attach the Restored Volume

1. Go to:

```text
EC2 → EBS → Volumes
```

2. Select the restored volume
3. Click:

```text
Actions → Attach volume
```

4. Select the EC2 instance
5. Use the recommended device name
6. Attach the volume

Connect to the EC2 instance and verify:

```bash
lsblk
```

If the volume contains a filesystem created before the snapshot, mount it without formatting it again.

```bash
sudo mount /dev/<device-or-partition> /mnt
```

> Do not run `mkfs` on a restored volume if you need the existing data.

---

## 13. Delete an EBS Volume

Before deleting a volume attached to an EC2 instance, detach it first.

1. Go to:

```text
EC2 → EBS → Volumes
```

2. Select the volume
3. Click:

```text
Actions → Detach volume
```

4. Wait until the volume is detached
5. Select the volume again
6. Click:

```text
Actions → Delete volume
```

---

## 14. Delete an EBS Snapshot

1. Go to:

```text
EC2 → EBS → Snapshots
```

2. Select the snapshot
3. Click:

```text
Actions → Delete snapshot
```

---

## 🧹 Cleanup Order

A recommended cleanup order is:

```text
Unmount Filesystem
        ↓
Detach Volume
        ↓
Delete Volume
        ↓
Delete Snapshot
        ↓
Terminate EC2 Instance (if no longer required)
```

---

## 📊 Complete Workflow

```text
Create EBS Volume
        ↓
Attach Volume to EC2
        ↓
Create Partitions
        ↓
Create XFS Filesystems
        ↓
Mount Partitions
        ↓
Configure /etc/fstab
        ↓
Create Snapshot
        ↓
Copy Snapshot to Another Region
        ↓
Create Volume from Snapshot
        ↓
Attach Restored Volume
        ↓
Verify Data
        ↓
Cleanup Resources
```

## 🛠️ Commands Used

```bash
lsblk
sudo blkid
cat /etc/fstab
sudo fdisk /dev/<device>
sudo mkfs.xfs /dev/<partition>
sudo mount <device> <mount-point>
sudo umount <mount-point>
sudo mount -a
df -h
```

## ⚠️ Important Notes

* EBS volumes are tied to a specific Availability Zone.
* An EBS volume can only be attached to an EC2 instance in the same Availability Zone.
* EBS snapshots can be copied between AWS Regions.
* A copied snapshot can be used to create a new volume in the destination Region.
* Use UUIDs in `/etc/fstab` instead of relying only on device names.
* Never run `mkfs` on a volume or partition containing data you need.
* Detach a volume before deleting it.
* Clean up unused volumes and snapshots to avoid unnecessary AWS charges.

## 👨‍💻 Author

**Shubham Patil**

---
