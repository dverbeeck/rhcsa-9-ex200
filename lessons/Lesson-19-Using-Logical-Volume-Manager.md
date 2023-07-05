# Lesson 19: Using Logical Volume Manager

- [Lesson 19: Using Logical Volume Manager](#lesson-19-using-logical-volume-manager)
    - [19.1 Understanding Advanced Storage Solutions](#191-understanding-advanced-storage-solutions)
      - [RHEL Advanced Storage Solutions](#rhel-advanced-storage-solutions)
    - [19.2 Exploring LVM Setup](#192-exploring-lvm-setup)
    - [19.3 Creating an LVM Logical Volume](#193-creating-an-lvm-logical-volume)
      - [LVM Creating Procedure Overview](#lvm-creating-procedure-overview)
      - [Understandig Extents](#understandig-extents)
    - [19.4 Understanding Device Mapper and LVM Device Names](#194-understanding-device-mapper-and-lvm-device-names)
      - [Understandig Device Mapper Names](#understandig-device-mapper-names)
    - [19.5 Resizing LVM Logical Volumes](#195-resizing-lvm-logical-volumes)
      - [Demo: Resizing a Logical Volume](#demo-resizing-a-logical-volume)
    - [19.6 Reducing Volume Groups](#196-reducing-volume-groups)
      - [Demo: Removing a VG from a PV](#demo-removing-a-vg-from-a-pv)
    - [Lesson 19 Lab: Managing LVM](#lesson-19-lab-managing-lvm)
    - [Lesson 19 Lab Solution: Managing LVM](#lesson-19-lab-solution-managing-lvm)

### 19.1 Understanding Advanced Storage Solutions

#### RHEL Advanced Storage Solutions

- LVM Logical Volumes
  - Used during default installation of RHEL.
  - Adds flexibility to storage (resize, snapshots and more).
- Stratis
  - Next generation Volume Managing Filesystem that uses thin provisioning by default.
  - Implemented in user space, which makes API access possible.
- Virtual Data Optimizer
  - Focused on storing files in the most efficient way.
  - Manages deduplicated and compressed storage pools.
  - Now Integrated in LVM.

### 19.2 Exploring LVM Setup

- Explain about lvm.

### 19.3 Creating an LVM Logical Volume

#### LVM Creating Procedure Overview

- Create a partition and set partition type to `lvm`.
- Use `pvcreate /dev/sdb1` to create the physical volume.
- Use `vgcreate vgdata /dev/sdb1` to create the volume group.
- Use `lvcreate -n lvdata -L 1G vgdata` to create the logical volume.
- Use `mkfs /dev/vgdata/lvdata` to create a filesystem.
- Put in /etc/fstab to mount it persistently.

#### Understandig Extents

- Extents are the elementay blocks of LVM allocation.
- The extent size can be defined while defining the volume group.
- Use `vgcreate -s 8M vgdata /dev/sdb1` to create a volume group with an extent size of 8MB.
- All of the LVM logical volumes built from the volume group will use the same extent size.
- Use `vgdisplay` to show properties of volume groups, including the extent size.

```bash
gdisk /dev/sdb;
?
n
enter
+2G
L
lvm
8e00
p
w

pvcreate /dev/sdb1;
vgcreate -s 8M vgdata /dev/sdb1;
vgs;
vgdisplay;
lvcreate -L 1G -n lvdata /dev/vgdata;
lvs;
mkfs.vfat /dev/vgdata/lvdata;
mkdir /lvdata
vim /etc/fstab;
/dev/vgdata/lvdata  /lvdata   vfat   defaults   0   0
Save and close the file.
mount -a;
```

### 19.4 Understanding Device Mapper and LVM Device Names

#### Understandig Device Mapper Names

- Device mapper is the system that the kernel uses to interface storage devices.
  - Used by LVM, Stratis, Multipath and more storage systems.
- Device mapper generates non-persistent names, like /dev/dm-0 and /dev/dm-1
- Persistent names are provided as sysmbolic links through /dev/mapper
  - /dev/mapper/vgdata-lvdata
- Alternatively, use the LVM generated symbolic links.
  - /dev/vgdata/lvdata

```bash
grep lvdata /etc/fstab;
ls -l /dev/mapper/vgdata-lvdata /dev/vgdata/lvdata;
```

### 19.5 Resizing LVM Logical Volumes

- Use `vgs` to verify the volume group has unused disk space.
- If required, use `vgextend` to add one or more PVs to the VG.
- Use `lvextend -r -L +1G` to grow the LVM logical volume, including the filesystem it's hosting.
  - `resize2fs` is a resize utility for Ext filesystem.
  - `xfs_growfs` can be used to grow an XFS filesystem.
- Shrinking is possible on Ext4 filesystems, not on XFS.

#### Demo: Resizing a Logical Volume

- Create 2 partitions with a size of 1 GiB each and set the `lvm` partition type.
- `vgcreate vgfiles /dev/sdb1`
- `lvcreate -l 255 -n lvfiles /dev/vgfiles`
- `mkfs.ext4 /dev/vgfiles/lvfiles`
- `mount /dev/vgfiles/lvfiles /mnt`
- `df -h | grep mnt` 
- `vgs` shows no available extents in the volume group.
- `vgextend vgfiles /dev/sde2` adds a new PV to the VG.
- `lvextend -r -l +50%FREE /dev/vgfiles/lvfiles` is now possible.
- `df -h | grep mnt` shows the newly available space in the volume.

### 19.6 Reducing Volume Groups

- If a volume group consists of multiple PVs, a PV can be removed from the VG if the remaining PVs have sufficient free space to allocate its extents.
- This procedure will not work if the remaining PVs are fully used already.
- First, you'll have to use pvmove to move used extents from the volume you want to remove to the remaining volume(s)
- Next, `vgreduce` can be used to complete the removal.

#### Demo: Removing a VG from a PV

- Use `fdisk` to create 2 partitions with a size of 2GB each and set the type to `lvm`
- `vgcreate vgdemo /dev/sdf1`
- `lvcreate -L 1G -n lvdemo /dev/vgdemo` # Do NOT use all space.
- `vgextend vgdemo /dev/sdf2`
- `pvs` shows all extents as available on /dev/sdf2
- `lvextend -L +500M /dev/vgdemo/lvdemo /dev/sdf2` ensure that free extents are used on sdf2 by adding the device name.
- `pvs` shows used extents on /dev/sdf2.
- `mkfs.ext4 /dev/vgdemo/lvdemo`

```bash
fdisk /dev/sdh;
g
n
enter
enter
+2G
t
lvm

n
enter
enter
+2G
t
2
lvm
w
vgcreate vgdemo /dev/sdh1;
lvcreate -L 1G -n lvdemo /dev/vgdemo;
vgs;
vgextend vgdemo /dev/sdh2;
pvs;
lvextend -L +500M /dev/vgdata/lvdemo /dev/sdh2;
pvs;
mkfs.ext4 /dev/vgdemo/lvdemo;
mount /dev/vgdemo/lvdemo /mnt;
df -h | grep mnt;
dd if=/dev/zero of=/mnt/bigfile bs=1M count=1100;
pvmove -v /dev/sdh2 /dev/sdh1;
vps;
vgreduce vgdemo /dev/sdh2;
pvs;
```

### Lesson 19 Lab: Managing LVM

To perform the tasks in this lab, add a new 10GiB disk to yoru virtual machine.

- Create an LVM logical volume with the name lvdb and a size of 1GiB. Also create the VG and PV that are required for this LV.
- Format this LV with the XFS filesystem and mount it persistently on /mounts/lvdb.

### Lesson 19 Lab Solution: Managing LVM

```bash
lsblk;

fdisk /dev/nvme0n2;
g
n
enter
enter
+1G
t
lvm
p
w

vgcreate vgdb /dev/nvme0n2p1;
lvcreate -L 1G -n lvdb /dev/vgdb; # If there is any error use this command `lvcreate -l 255 -n lvdb /dev/vgdb;`
mkfs.xfs /dev/mgdb/lvdb;
mkdir /mounts/lvdb;
vim /etc/fstab;
/dev/vgdb/lvdb  /mounts/lvdb   xfs   defaults  0   0
mount -a;
```
