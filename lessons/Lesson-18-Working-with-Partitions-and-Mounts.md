# Lesson 18: Working with Partitions and Mounts

- [Lesson 18: Working with Partitions and Mounts](#lesson-18-working-with-partitions-and-mounts)
    - [18.1 Understanding Disk Layout](#181-understanding-disk-layout)
    - [18.2 Exploring Linux Storage Options](#182-exploring-linux-storage-options)
      - [Linux Storage Options](#linux-storage-options)
      - [Linux Block Devices](#linux-block-devices)
      - [Understandig Partition Numering](#understandig-partition-numering)
    - [18.3 Understanding GPT and MBR Partitions](#183-understanding-gpt-and-mbr-partitions)
    - [18.4 Creating Partitions with fdisk](#184-creating-partitions-with-fdisk)
      - [Patitioning Tools](#patitioning-tools)
      - [Understandig Partition Type](#understandig-partition-type)
    - [18.5 Creating and Mounting File Systems](#185-creating-and-mounting-file-systems)
      - [Understandig Linux Filesystem](#understandig-linux-filesystem)
      - [Creating Filesystem](#creating-filesystem)
      - [Mounting Filesystem](#mounting-filesystem)
    - [18.6 Mounting Partitions through /etc/fstab](#186-mounting-partitions-through-etcfstab)
      - [Understandig /etc/fstab](#understandig-etcfstab)
      - [Avoiding Problms While Booting](#avoiding-problms-while-booting)
    - [18.7 Using UUID and Labels](#187-using-uuid-and-labels)
      - [Labels and UUIDs](#labels-and-uuids)
      - [Managing Persistent Naming Attributes](#managing-persistent-naming-attributes)
      - [XFS UUIDs on Cloned Devices](#xfs-uuids-on-cloned-devices)
    - [18.8 Defining Systemd Mounts](#188-defining-systemd-mounts)
      - [Systemd Mounts](#systemd-mounts)
    - [18.9 Creating a Swap Partition](#189-creating-a-swap-partition)
      - [Managing swap](#managing-swap)
    - [Lesson 18 Lab: Managing Partitions](#lesson-18-lab-managing-partitions)
    - [Lesson 18 Lab Solution: Managing Partitions](#lesson-18-lab-solution-managing-partitions)

### 18.1 Understanding Disk Layout

```bash
lsblk;
cat /proc/partitions
fdisk -l /dev/sda
```

### 18.2 Exploring Linux Storage Options

#### Linux Storage Options

- Partitions
  - Used to allocate dedicated storage to specific types of data.
- LVM Logical Volumes
  - Used at default installation of RHEL
  - Adds flexibility to storage (resize, snapshots and more)
- Straits
  - Next generation Volume Managing Filesystem that uses thin provisioning by default.
  - Implemented in user space, which makes API access possible.

#### Linux Block Devices

- According to the driver that is used, different block devices can be used.
- Use `lsblk` to get an overview of currently existing devices.
- `/dev/sdx` is common for SCSI and SATA disks.
- `/dev/vdx` is used in KVM virtual machines.
- `/dev/nvmexny` is used for NVME devices.

#### Understandig Partition Numering

- On `sd` and `vd` devices, partitions are numbered in order
  - /dev/sda2 is the second partition on the first hard disk.
  - /dev/sdb1 is the first partition on the second hard disk.
- On `nvme` devices, partition names are appended to the device name.
  - /dev/nvme0n1p1 is the first partition on the first hard disk.
  - /dev/nvme0n3p2 is the second partition on the third hard dis
- Use `lsblk` to avoid confusion
  - `lsblk` reads the kernel partition table in /proc/partitions

### 18.3 Understanding GPT and MBR Partitions

- Master Boot Record (MBR) is part of the 1981 PC specification.
- 512 bytes to store boot information.
- Place for 4 partitions only with a max size of 2 TiB.
- To use more partitions, extened and logical partitions must be used.- GUID Partition Table (GPT) is a newer partition table (2010)
  - More space to store partitions.
  - Used to overcome MBR Limitations.
  - 128 partitions max.

### 18.4 Creating Partitions with fdisk

#### Patitioning Tools

- `fdisk` has been around since the earliest Linux versions.
  - Offers easy access to advanced features.
  - Completely reworked to support MBR and GPT partitions.
  - On new disks, create a GPT partition table using `g`
- `gdisk` was introduced to offer GPT support
  - Creates GPT partition table by default
- `parted` was introduced to make partitioning easier
  - Can be scripted easily.
  - Advanced features are a bit hidden.

#### Understandig Partition Type

- A partition type is a low-level identifier of the operating system, filesystem and boot manager using a specific partition
- Partition types were important in the past, currently partitions will work if the wrong partition type is set.
  - In `fdisk` use `t` to change the partition type
- Use Aliases to set the appropriate partition type:
  - `linux` - standard Linux
  - `swap` - linux swap
  - `uefi` - udfi boot
  - `lvm` - logical volume manager
- Other partition types are available, use `l` for a list.

```bash
lsblk;
fdisk /dev/sda
p
m
n
p
enter
enter
+1G
p
w
lsblk;

# Create an extended partition
fdisk /dev/sda
n
e
enter
enter
enter
n
enter
+2G
n
enter
+1G
p
w

lsblk;
```

### 18.5 Creating and Mounting File Systems

#### Understandig Linux Filesystem

- XFS is the default filesystem.
  - Fast and scalable.
  - Uses CoW to guarantee data integrity.
  - Size can be increased, not decreased.
- Ext4 was default in RHEL6 and is still used.
  - Backward compatible to Ext2
  - Uses Journal to guarantee data integrity.
  - Size can be increased and decreased.   
- vfat offers multi-OS supprt
  - Used for shared devices
- Btrfs is a new and advanced filesystem.
  - Not installed by default on RHEL.

#### Creating Filesystem

- `mkfs.xfs` creates an XFS filesystem.
- `mkfs.ext4` creates an Ext4 filesystem.
- `mkfs.vfat` creates a vfat filesystem.
- Use `mkfs.[Tab][Tab]` to show a list of available filesystems.
- Do NOT use `mkfs` as it will create an Ext2 filesystem.

#### Mounting Filesystem

- After making the filesystem, you can mount it in runtime using the `mount` command.
  - `mount /dev/vdb1 /mnt`
- Use `umount` before disconnecting a device.
  - Use `lsof /mnt` if you get an error about open files.
- The `mount` command shows all mounted filesystems, including administrative kernel mounts.
- The `findmnt` command shows which filesystem is mounted where in the directory structure.

```bash
lsblk;
mkfs.xfs /dev/sda3;
mount /dev/sda3 /mnt;
lsblk;
mount;
mount | grep '^/'
findmnt;
cd /mnt;
umount /mnt;
lsof /mnt;
cd
lsof /mnt;
umount /mnt;
mount /dev/sda5 /mnt; # should not mount, because it is not formated with any filesystem.
```

### 18.6 Mounting Partitions through /etc/fstab

#### Understandig /etc/fstab

- /etc/fstab is the main configuration file to persistently mount partitions.
  - `/dev/sdb1   /data   ext4  defaults  0 0`
- /etc/fstab content is used to generate systemd mounts by the `systemd-fstab-generator` utility.
- To update systemd mounts, it is recommended to use `systemctl daemon-reload` after edition /etc/fstab

#### Avoiding Problms While Booting

- If an error was found in /etc/fatab, yoru system will drop a Troubleshooting shell while booting.
- After adding lines to /etc/fstab, use `mount -a` to mount all that hasn't been mounted yet.
- Alternatively, use `findmnt --verify` to verify syntax.

```bash
mkdir /mydata;
vim /etc/fstab;

/dev/sda3 /mydata xfs   defaults  0   0
Save and close the file.
```

### 18.7 Using UUID and Labels

#### Labels and UUIDs

In datacenter environments, block device names may change.
Different solutions exist for persistent naming.
- `UUID`: a UUID is automatically generated for each device that contains a filesytem or anything similar.
- `Label`: while creating the filesystem, the option `-L` can be used to set an arbitrary name that can be used for mounting the filesystem.
- Unique device names are created in /dev/disk/*

#### Managing Persistent Naming Attributes

- `blkid` shows all devices with their naming attributes.
- `tune2fs -L` is used to set a label on an Ext filesystem.
- `xfs_admin -L` is used to set a label on an XFS filesystem.
- `mkfs.* -L` is used to set a lable while creating the filesystem.

#### XFS UUIDs on Cloned Devices

- UUID are supposed to be unique.
- If an XFS filesytem is cloned, the UUID is cloned as well.
- Use `xfs_admin -U generate /dev/sdb1` to generate a new UUID.

```bash
fdisk /dev/sda;
p # Create a new partition if no extra partition doesn't exist.
q
mkfs.xfs /dev/sda5;
mkfs.ext4 /dev/sda6;
mkdir /xfs /ext4;
vim /etc/fstab;
/dev/sda5 /xfs  xfs   defaults  0   0
/dev/sda5 /ext4  ext4   defaults  0   0
mount -a;
lsblk;
touch /ext4/ext4file
touch /xfs/xfsfile
umount /xfs
vim /etc/fstab
# /dev/sda5 /xfs  xfs defaults  0 0
fdisk /dev/sda
p
d
5
w
reboot;

# Troubleshooting commands.
lsblk;
blkid;
blkid | grep sda5 | awk '{ print $2}'
cp /etc/fstab /etc/fstab.org;
blkid | grep sda5 | awk '{ print $2}' >> /etc/fstab;
vim /etc/fstab;

UUID=xxxxx  /ext4   ext4  defaults   0   0
save the file and close it.
mount -a;
lsblk;
Ctrl+D

# Labels
fdisk /dav/sda;
xfs_admin -L cow /dev/sda6;
vim /etc/fstab;
LABEL=cow /xfs xfs  defaults  0   0
mount -a;
ls /xfs;
```

### 18.8 Defining Systemd Mounts

#### Systemd Mounts

- Lines in /etc/fstab are converted to systemd mounts.
  - Check /run/systemd/generator for the automatically generated files.
- Mounts can be created using systemd.mount files.
- Using .mount files allows you to be more specific in defining dependencies.
- Use `systemctl cat tmp.mount` for an example.

```bash
cd /run/systemd/;
cd generator;
ls;
vim mydata.mount;
systemctl cat tmp.mount;
mount | grep tmp;
systemctl enable --now tmp.mount;
mount | grep tmp;
grep tmp /etc/fstab;
systemctl status tmp.mount;
ls /tmp;
systemctl disable --now tmp.mount;
```

### 18.9 Creating a Swap Partition

#### Managing swap

- Swap is RAM that is emulated on disk.
- All Linux systems should have a tleast some swap.
  - The amount of swap depends on the use of the server.
- Swap can be created on any block device, including swap files.
- Set partition type to linux-swap.
- After creating the swap partition, use `mkswap` to create the swap FS.
- Activate using `swapon`

```bash
fdisk /dev/sda
n
enter
+2G
p
t
7 #  Choose the partition number
swap
p
m
x
m
f
r
p
w
reboot;
lsblk;
fdisk -l /dev/sda;
free -m;
mkswap /dev/sda7;
free -m;
swapon /dev/sda7;
free -m
vim /etc/fstab;
/dev/sda7 none  swap  defaults  0   0
Save and close the file.
swapoff /dev/sda7;
swapon -a
free -m;
```

### Lesson 18 Lab: Managing Partitions

To work on this lab, you'll need to create a 10GiB additional hard disk on your virtual machine. This disk needs to be completely available.

- Create a primary partition with a size of 1GiB. Format it with Ext4, and mount it persistently on /mounts/files, using its UUID.
- Create an extended partition that includes all remaining disk space. In this partition, create a 500MiB XFS partition and mount it persistently on /mounts/xfs, using the label myxfs.
- Create a 500MiB swap partition and mount it persistently.   

### Lesson 18 Lab Solution: Managing Partitions

```bash
lsblk;
fdisk /dev/nvme0n1
# Create a primary partition.
n
p
enter
enter
+1G

# Create an extended partition.
n
e
enter
enter
enter
# Create a logical partition.
n
enter 
+500M

n
enter
+500M
t
6
swap
p
w

mkdir -p /mounts/files /mounts/xfs
mkfs.ext4  /dev/nvme0n1p1
mkfs.xfs  -L myxfs  /dev/nvme0n1p5
mkswap /dev/nvme0n1p6
blkid; # Copy the UUID of the primary partition.
vim /etc/fstab;
<paste the UUID here> /mounts/files ext4  defaults  0   0
LABEL=myxfs /mounts/xfs xfs defaults  0   0
<swap partition UUID>   none  swap  defaults  0   0
save and close the file.
findmnt --verify;
reboot;
lsblk;
```
