# Lesson 6: Using Essential File Management Tools

- [Lesson 6: Using Essential File Management Tools](#lesson-6-using-essential-file-management-tools)
    - [6.1 Exploring the Filesystem Hierarchy](#61-exploring-the-filesystem-hierarchy)
      - [The Filesytem Hierarchy Standard](#the-filesytem-hierarchy-standard)
    - [6.2 Using Essential File Management Commands](#62-using-essential-file-management-commands)
      - [Essential Commands](#essential-commands)
    - [6.3 Finding Files](#63-finding-files)
      - [Finding Files](#finding-files)
    - [6.4 Mounting Filesystems](#64-mounting-filesystems)
      - [Using mount](#using-mount)
    - [6.5 Using Links](#65-using-links)
      - [Understanding Links](#understanding-links)
    - [6.7 Working with Compressed Files](#67-working-with-compressed-files)
      - [Using Compression](#using-compression)
    - [Lesson 6 Lab: Using Essential File Management Tools](#lesson-6-lab-using-essential-file-management-tools)
    - [Lesson 6 Lab Solution: Using Essential File Management Tools](#lesson-6-lab-solution-using-essential-file-management-tools)

### 6.1 Exploring the Filesystem Hierarchy

#### The Filesytem Hierarchy Standard

- Directory usage on Linux is highly standardized.
- Standard directories are defined in the Filesystem Hierarchy Standard (FHS), which is maintained by Linux Foundation.
- The starting point of the filesystem is the root directory.
- Different devices may be intergrated in the filesystem by using mounts.

```bash
cd /
cd /etc
ls
cd /usr
ls
cd /
cd var
ls
cd log
ls -l
cd /tmp
touch hello
ls -l
cd
pwd
man hier
```

### 6.2 Using Essential File Management Commands

#### Essential Commands

- **ls** list files
- **mkdir** make a directory
- **cp** copy files
- **mv** move files
- **rmdir** remove an empty directory
- **rm** remove files

```bash
mkdir filedemo
ls -lrt 
cp output filedemo/
ls filedemo/
ls -dl filedemo/
mv 
rmdir
rm
```

### 6.3 Finding Files

#### Finding Files

- **ls** is used to list files, not to find files.
- **which** looks for binaries in $PATH
- **locate** uses a database, built by **updatedb** to find files in a database.
- **find** is the most flexible tool that allows you to find files based on many criteria.

```bash
which
locate
updatedb

sudo -i
fine / -name "hosts" 2> /dev/null
find / -type f -size +100M
find /etc -size +1k -exec grep -l student {} \; 2> /dev/null
find /etc -exec grep -l student {} \; -exec cp {} find/contents/ \; 2> /dev/null
find /etc/ -name '*' -type f | xargs grep "127.0.0.1"
```

### 6.4 Mounting Filesystems

#### Using mount

- To access a device, it must be connected to a directory.
- This is known as mounting the device.
- The Linux filesystem typically uses multiple mounts.
- Different types of data typically are on different devices for multiple reasons.
  - security
  - manageability
  - specific mount options
- To mount a device, use **mount /dev/devicename /directory**
  - mount /dev/sdb1 /mnt
- The **findmnt** command shows all currently mounted devices and their place in the filesystem.

```bash
findmnt
lsblk
mount /dev/sda1 /mnt
ls /mnt
umount /mnt
```

### 6.5 Using Links

#### Understanding Links

- Links are pointers to files in a different location.
- Compare to shortcuts on other operating systems.
- Links can be useful to make the same file available on multiple locations.
- Linux uses **hard links** and **symbolic links**
- Create hard links with **ln** and symbolic links with **ln -s**

    ```bash
    ln
    ln -s

    ls -il /etc/hosts
    ln /etc/hosts myhosts
    ls -il /etc/hosts myhosts

    ln -s myhosts syshosts
    ls -il /etc/hosts myhosts symhosts
    rm myhosts
    ls -il symhosts

    ln /etc/hosts myhosts
    ls -il symhosts
```

### 6.6 Archiving Files

```bash
tar
tar -cvf file.tar /home /etc
tar -tvf file.tar
tar -xvf file.tar
- To add compression, use -z, -j or -J

tar -cvf /tmp/archive.tar /home /etc
ls -l /tmp/archive.tar
tar -czvf /tmp/archive.tgz /home /etc
ls -l /tmp/archive.tgz
tar -cjvf /tmp/archive.tar.bz2 /home /etc
ls -l /tmp/archive.tar.bz2
tar -cJvf /tmp/archive.tar.xz /home /etc
ls -l /tmp/archive.tar.xz
ls -l archive.t*
tar -tvf archive.tar
tar -xvf archive.tar
```

### 6.7 Working with Compressed Files

#### Using Compression

- **gzip (-z)** is still the most common compression utility.
- **bzip2 (-j)** is an alternative utility.
- **zip** is also available and has Windows-compatibile syntax.
- **xz (-J)** is showing up more often as well.

### Lesson 6 Lab: Using Essential File Management Tools

- Use **tar** to create a compressed archive of all files in the /etc/and /opt directories. Write this archive to your home directory.
- Create a symbolic link to thte archive you've just created in the /tmp directory.
- Remove the archive from your home directory. What happens to the symbolic link?

### Lesson 6 Lab Solution: Using Essential File Management Tools

```bash
tar -czvf lab.tgz /etc/ /opt/
tar -tvf lab.tgz
ln -s lab.tgz /tmp/lab.link
rm lab.tgz
```
