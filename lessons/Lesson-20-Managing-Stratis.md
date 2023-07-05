# Lesson 20: Managing Stratis

- [Lesson 20: Managing Stratis](#lesson-20-managing-stratis)
    - [20.1 Understanding the Storage Stack](#201-understanding-the-storage-stack)
      - [Understandig LVM Usage Options](#understandig-lvm-usage-options)
    - [20.2 Creating Stratis Volumes](#202-creating-stratis-volumes)
      - [Understandig Stratis](#understandig-stratis)
      - [Managing Straits](#managing-straits)
      - [Demo: Managing Stratis Volumes](#demo-managing-stratis-volumes)
    - [20.3 Using Stratis Snapshots](#203-using-stratis-snapshots)
      - [Understandig Stratis Snapshots](#understandig-stratis-snapshots)
      - [Demo: Using Stratis Snapshots](#demo-using-stratis-snapshots)
    - [Lesson 20 Lab: Managing Stratis](#lesson-20-lab-managing-stratis)
    - [Lesson 20 Lab Solution: Managing Stratis](#lesson-20-lab-solution-managing-stratis)

### 20.1 Understanding the Storage Stack

- Explain about storage.
- The block device layer allows RHEL to use different storage solutions by including the appropriate driver in the Linux kernel.
- Multipath is an optional component that is used in storage network to provide redundancy to different block devices.
- Paritions make it possible to allocate different storage units on a block device
- RAID allows for the creation of redundant storage volumes.
- LVM creates dynamic volumes from one or more block devices.
- Storage volumes can be used for different purposes.
  - File systems.
  - Database.
  - Ceph OSDs.

#### Understandig LVM Usage Options

- LVM can be used with LUKS encryption to provide device-based block encryption.
- LVM can be used with VDO deduplication and compression as a feature of logical volumes.
- LUKS and VDO can be used without LVM also.

### 20.2 Creating Stratis Volumes

#### Understandig Stratis

- Straits volumes always use the XFS filesystem.
- Straits volumes are thin provisioned by nature.
- Volume storage is allocated from the straits pool.
- Each straits volume needs a minimal size of 4 GiB.
- Because of the thin provisioning, straits tools must be used to monitor available storage space.

#### Managing Straits

- To work with straits, you'll need the `stratised` and `stratis-cli` package.
- Use the `stratis` command to create pools and filesystems.
- This command has awssome tab completion.
- While working with stratis, use `stratis pool list` to ensure the pool has suficient space remaining.
- To mount stratis volumes:
  - Use UUID.
  - include `x-systemd.requires=stratisd.service` as a mount option.

#### Demo: Managing Stratis Volumes

```bash
dnf install stratis-cli stratisd;
systemclt enable --now stratisd;
stratis pool create mypool /dev/sdb;
stratis pool list;
stratis pool add-data mypool /dev/sdc; # Extend stratis. 
stratis blockdev list;
stratis fs create mypool myfs;
mkdir /myfs;
stratis fs list;

lsblk --output=UUID /dev/stratis/mypool/myfs >> /etc/fstab;
vim /etc/fstab;

UUID=xxxxx /myfs xfs  x-systemd.requires=stratisd.service    0   0
Save and close the file.
mount -a;
mount;
df -h;
stratis pool list;
```

### 20.3 Using Stratis Snapshots

#### Understandig Stratis Snapshots

- A stratis snapshot is a metadata copy that allows you to access the state of the snapshot at any time.
- A snapshot is NOT a backup, but can be helpful in accessing deleted files.
- A snapshot is mounted by its device name, not bu UUID.

#### Demo: Using Stratis Snapshots

```bash
dd if=/dev/zero of=/myfs/bigfile bs=1M count=2000
stratis pool list;
stratis fs list;
stratis fs snapshot mypool myfs myfs-snap;
stratis fs list;
rm /myfs/bigfile;
mkdir /myfs-snap;
mount /dev/stratis/mypool/myfs-snap /myfs-snap;
ls -l /myfs-snap/
umount /myfs-snap;
stratis fs destory mypool myfsf-snap;
```

### Lesson 20 Lab: Managing Stratis

- Create a stratis pool with a size of 10GiB, with the name sratispool, containing 2 filessystems; myfiles and myprograms.
- Mount these volumes persistently on /myfiles and /myprograms.
- Copy all files from /etc/ that have a name starting with an a, c or f to /myfiles.
- Create a snapshot of the myfiles filesystem.
- Delete all files from /myfiles that have a name starting with an a.
- Verify that you can still access these files from the snapshot.


### Lesson 20 Lab Solution: Managing Stratis

```bash
lsblk;
stratis pool create pool20 /dev/sde;
stratis pool list;
stratis fs create pool20 myfiles;
stratis fs create pool20 myprograms;
stratis fs list;
stratis fs list | awk '/prgra/ { print $NF }' # NF means last column.
stratis fs list | awk '/prgra/ { print $NF }' >> /etc/fstab
stratis fs list | awk '/files/ { print $NF }' >> /etc/fstab
vim /etc/fstab;
UUID=<uuid-goes-here>   /myfiles  xfs x-systemd.requires=stratisd.service    0   0
UUID=<uuid-goes-here>   /myprograms  xfs x-systemd.requires=stratisd.service    0   0
save and close the file.
mkdir /myfiles /myprograms;
mount -a;
df -h;
lsblk;
stratis fs list;
cp /etc/[acf]* /myfiles;
ls /myfiles;
stratis fs snapshot pool20 myfiles myfiles-snap;
mkdir /myfiles-snap;
rm /myfiles/a* -f;
mount /dev/stratis/pool20/myfiles-snap /myfiles-snap;
ls /myfiles/a*
```
