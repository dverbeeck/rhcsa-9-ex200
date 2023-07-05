# Lesson 32: Hands-on RHCSA Practice Exam

- [Lesson 32: Hands-on RHCSA Practice Exam](#lesson-32-hands-on-rhcsa-practice-exam)
    - [32.1 Exploring RHCSA Practice Exam Assignments](#321-exploring-rhcsa-practice-exam-assignments)
      - [Setting up a Base Server](#setting-up-a-base-server)
      - [Resetting the Root Password](#resetting-the-root-password)
      - [Configure a Repository](#configure-a-repository)
      - [Managing Partitions](#managing-partitions)
      - [Managing LVM Logical Volumes](#managing-lvm-logical-volumes)
      - [Creating Users and Groups](#creating-users-and-groups)
      - [Managing Permissions](#managing-permissions)
      - [Scheduling Jobs](#scheduling-jobs)
      - [Managing Containers as Service](#managing-containers-as-service)
      - [Managing Automount](#managing-automount)
      - [Setting Time](#setting-time)
      - [Managing SELinux](#managing-selinux)
    - [32.2 Setting up a Base Server Solution](#322-setting-up-a-base-server-solution)
    - [32.3 Resetting the Root Password Solution](#323-resetting-the-root-password-solution)
    - [32.4 Configuring a Repository Solution](#324-configuring-a-repository-solution)
    - [32.5 Managing Partitions Solution](#325-managing-partitions-solution)
    - [32.6 Managing LVM Logical Volumes Solution](#326-managing-lvm-logical-volumes-solution)
    - [32.7 Creating Users and Groups Solution](#327-creating-users-and-groups-solution)
    - [32.8 Managing Permissions Solution](#328-managing-permissions-solution)
    - [32.9 Scheduling Jobs Solution](#329-scheduling-jobs-solution)
    - [32.10 Managing Containers as Services Solution](#3210-managing-containers-as-services-solution)
    - [32.11 Managing Automount Solution](#3211-managing-automount-solution)
    - [32.12 Setting Time Solution](#3212-setting-time-solution)
    - [32.13 Managing SELinux Solution](#3213-managing-selinux-solution)

### 32.1 Exploring RHCSA Practice Exam Assignments

#### Setting up a Base Server
  
- Install two servers using the minimal installation pattern. Use the names **server1.example.com** and **server2.example.com** and use DHCP to get an IP address from the local DNS server. Ensure that the partition for **/** is **15 GiB**. Also create a **1 GiB swap** partition. Do NOT register the servers with Red Hat. 

#### Resetting the Root Password

- Use the appropriate solution to reset the root password on server2, assuming that you have lost the root password and you have no administrator access to the server anymore.

#### Configure a Repository

- On both servers, create an ISO file based on the installation DVD. Mount this ISO file persistently and configure the servers to use the local ISO file as the repositories. After successfully completing this assignment, you should be able to install software on both servers.

#### Managing Partitions 

- On server1, use your virtualization software to increase the size of your primary disk in such a way that at least **10 GiB** of unallocated disk space is available.
- In the free disk space, create a **1 GiB** partition and format it with the **vfat** filesystem. Make sure it is mounted persistently on the **/winfile** directory.  
- Also create a **1 GiB** swap partition and ensure it is mounted persistently.

#### Managing LVM Logical Volumes

- Create a logical volume with the name myfiles. Ensure it uses** 8 MiB** extents. Configure the volume to use** 75 extent**s. Format it with the **ext4 file system** and ensure it mounts persistently on **/myfiles**
- Increase the size of the **/** logical volume by **5 GiB**
- If volume groups need to be created, create them as needed.

#### Creating Users and Groups
  
-  Create a user **anil**. Ensure she has the password set to "**password**" and is using UID **1234**. She must be a member of the secondary group sales.
- Create a user **myapp**. Ensure this user cannot open an interactive shell.

#### Managing Permissions

- Create a shared group directory **/sales** and ensure that **anil** is the owner of that directory. The owner and the group sales should have permissions to access this directory and read and write files in it. Other users should have no permissions at all. Ensure that any new file that is created in this directory is group-owned by the group sales automatically.

#### Scheduling Jobs
 
- Schedule a job that writes "hello folks" to syslog every Monday through Friday at 2 AM. Make sure this job is executed as the user **anil**.

#### Managing Containers as Service
 
- Create a container with the name **mydb** that runs the **mariadb** database as user **anil**. The container should automatically be started at system start, regardless of whether or not user **anil** is logging in. The container further should meet the following requirements.
  - The host directory **/home/anil/mydb** is mounted on the container directory **/var/lib/mysql**
  - The container is accessible on host port **3206**
  - You do not have to create any databases in it.

#### Managing Automount
 
- On server2, create the directories **/homes/user1** and **/homes/user2**. Use NFS to share these directories and ensure the firewall does not block access to these directories.
- On server1, create a solution that automatically, on-demand mounts **server2:/homes/user1** on **/homes/user1**, and also that automatically, on-demand mounts **server2:/homes/user2** on **/homes/user2** when these directories are accessed.

#### Setting Time
  
- Configure server1 and server2 as an NTP client for pool.ntp.org

#### Managing SELinux
  
- Ensure that the Apache web server is installed and configure it to offer access on port 82.
- Copy the file /etc/hosts to /tmp/hosts. Next, move /tmp/hosts to the directory /var/www/html/hosts and ensure this file can be accessed by the Apache web server.

### 32.2 Setting up a Base Server Solution

- To be updated. # TODO

### 32.3 Resetting the Root Password Solution

- Use the appropriate solution to reset the root password on server2, assuming that you have lost the root password and you have no administrator access to the server anymore.
  
  - Enter into the Grub menu while booting.
  - Find the line that loads the Linux kernel and add `init=/bin/bash` to the end of the line.
  - Press Ctrl+x
  - mount -o remount,rw /
  - touch aaa # Test write permission.
  - passwd root
  - touch /.autorelabel
  - exec /usr/lib/systemd/systemd

### 32.4 Configuring a Repository Solution

- On both servers, create an ISO file based on the installation DVD. Mount this ISO file persistently and configure the servers to use the local ISO file as the repositories. After successfully completing this assignment, you should be able to install software on both servers.
  
```bash
ssh anil@server1;
sudo -i;
hostnamectl set-hostname server1.example.com;
exit
sudo -i;
hostname;

lsblk;
dd if=/dev/sr0 of=/rhel9.iso bs=1M;
mkdir /repo
cp /etc/fstab /etc/fstab.org;
vi /etc/fstab;
/rhel9.iso  /repo iso9660   defaults    0   0
Save and close the file.

mount -a;
ls /repo;

cat >> /etc/yum.repos.d/base.repo <<EOF
[Base]
name=BaseOS
baseurl=file:///repo/BaseOS
gpgcheck=0
EOF

dnf repolist;

cat >> /etc/yum.repos.d/appstream.repo <<EOF
[AppStream]
name=AppStream
baseurl=file:///repo/AppStream
gpgcheck=0
EOF

dnf repolist;
dnf install -y vim;

# Note: repeat the same steps on the server2.exmaple.com as well.
```

### 32.5 Managing Partitions Solution

- On server1, use your virtualization software to increase the size of your primary disk in such a way that at least **10 GiB** of unallocated disk space is available.
- In the free disk space, create a **1 GiB** partition and format it with the **vfat** filesystem. Make sure it is mounted persistently on the **/winfile** directory.  
- Also create a **1 GiB** swap partition and ensure it is mounted persistently.

```bash
lsblk;
fdisk /dev/sda;
p
n
p
3
enter
+1G
n
e
enter
enter
n
enter
+1G
t
5
swap
p
w

lsblk;
mkfs.vfat /dev/sda3; # Install package if vfat not found.
dnf provides */mkfs.vfat;
dnf install -y dosfstools;
mkfs.vfat /dev/sda3;
mkswap /dev/sda5;

cp /etc/fstab /etc/fstab.org
vim /etc/fstab
/dev/sda3 /winfile    vfat  defaults 0  0
/dev/sda5  nano     swap    defaults  0 0 

mkdir /winfile;
mouna -a;
swapon -a
findmnt --verify;
reboot;
```

### 32.6 Managing LVM Logical Volumes Solution

- Create a logical volume with the name myfiles. Ensure it uses** 8 MiB** extents. Configure the volume to use** 75 extent**s. Format it with the **ext4 file system** and ensure it mounts persistently on **/myfiles**
- Increase the size of the **/** logical volume by **5 GiB**
- If volume groups need to be created, create them as needed.
  
```bash
sudo -i;
lsblk;
echo $(( 8 * 75 ))
fdisk /dev/sda;
enter
enter
t
6
lvm
p
w

vgcreate -s 8M vglab /dev/sda6; # If vgcreate command not found, install the supported package.
dnf provides */vgcreate;
dnf install -y lvm2;

vgcreate -s 8M vglab /dev/sda6;
vgs;
lvcreate -l myfiles -l 75 /dev/vglab;
lvs;
mkfs.ext4 /dev/vglab/myfiles;
mkdir /myfiles;
vim /etc/fstab;
/dev/vglab/myfiles  /myfiles  ext4  defaults  0  0
save and close the file.

mouna -a;
# Increase the size of the "/" logical volume by "5 GiB"
lvextend -r -L +5G /dev/vglab/myfiles
df -h /
```

### 32.7 Creating Users and Groups Solution

-  Create a user **anil**. Ensure she has the password set to "**password**" and is using UID **1234**. She must be a member of the secondary group sales.
- Create a user **myapp**. Ensure this user cannot open an interactive shell.

```bash
groupadd sales;
useradd -u 1234 -G sales anil;
id anil;
passwd anil;
which nologin;
useradd -s /usr/sbin/nologin myapp;
id myapp;
grep myapp /etc/passwd
```

### 32.8 Managing Permissions Solution

- Create a shared group directory **/sales** and ensure that **anil** is the owner of that directory. The owner and the group sales should have permissions to access this directory and read and write files in it. Other users should have no permissions at all. Ensure that any new file that is created in this directory is group-owned by the group sales automatically.

```bash
mkdir /sales;
chown anil.sales /sales;
chmod 2770 /sales;
ls -ld /sales;
```

### 32.9 Scheduling Jobs Solution

- Schedule a job that writes "hello folks" to syslog every Monday through Friday at 2 AM. Make sure this job is executed as the user **anil**.
  
```bash
su - anil;
crontab -e;
0 2 * * Mon-Fri logger  "hello folks"
```

### 32.10 Managing Containers as Services Solution

- Create a container with the name **mydb** that runs the **mariadb** database as user **anil**. The container should automatically be started at system start, regardless of whether or not user **anil** is logging in. The container further should meet the following requirements.
  - The host directory **/home/anil/mydb** is mounted on the container directory **/var/lib/mysql**
  - The container is accessible on host port **3206**
  - You do not have to create any databases in it.

```bash
loginctl enable-linger anil;
exit;
ssh anil@server2;
mkdir mydb;
podman unshare chown 27:27 mydb/ # Install the package if the command not found.
dnf provides */podman;
dnf install -y container-tools;
podman unshare chown 27:27 mydb/

podman run -d -p 3206:3206 --name mydb -v /home/anil/mydb:/var/lib/mysql:Z -e MYSQL_ROOT_PASSWORD=password quay.io/centos7/mariadb-103-centos7;

podman ps;
mkdir -p ~/.config/systemd/user;
cd ~/.config/systemd/user;
podman generate systemd --name mydb --files --new;
systemctl --user daemon-reload;
systemctl --user enable container-mydb.service;
sudo reboot;
ssh root@server2;
ps faux
/anil
```

### 32.11 Managing Automount Solution

- On server2, create the directories **/homes/user1** and **/homes/user2**. Use NFS to share these directories and ensure the firewall does not block access to these directories.
- On server1, create a solution that automatically, on-demand mounts **server2:/homes/user1** on **/homes/user1**, and also that automatically, on-demand mounts **server2:/homes/user2** on **/homes/user2** when these directories are accessed.

```bash
ssh anil@server2;
dnf install -y autofs firewalld nfs-utils;
mkdir -p /homes/user{1,2}
vim /etc/exports
/homes/ *(rw,no_root_squash)

save and close the file.

systemctl enable --now nfs-server;
for i in rpc-bind mountd nfs; do firewall-cmd --add-service $i --permanent; done
firewall-cmd --reload;
ip a;

# Update the server1 and server2 /etc/hosts file  
ssh anil@server1;
dnf install -y autofs nfs-utils;
showmount -e server2;
vim /etc/auto.master;
/homes  /etc/auto.homes;
save and close the file.
vim /etc/auto.homes
*   -rw     server2:/homes/&
save and close the file.

systemclt enable --now autofs;
cd /homes/user1
cd ../user2
```

### 32.12 Setting Time Solution

- Configure server1 and server2 as an NTP client for pool.ntp.org

```bash
ssh anil@server2;
vim /etc/chrony.conf;
pool poolntp.org iburst

save and close the file.
systemclt restart chronyd;
chronyc sources;

ssh anil@server1;
vim /etc/chrony.conf;
pool poolntp.org iburst

save and close the file.
systemclt restart chronyd;
chronyc sources;
```

### 32.13 Managing SELinux Solution

- Ensure that the Apache web server is installed and configure it to offer access on port 82.
- Copy the file /etc/hosts to /tmp/hosts. Next, move /tmp/hosts to the directory /var/www/html/hosts and ensure this file can be accessed by the Apache web server.

```bash
ssh anil@server1;
dnf install -y httpd;
vim /etc/httpd/conf/httpd.conf;
DocumentRoot "/web"
<Directoy  "/web">
save and close the file.

mkdir /web/
echo "hello" > /web/index.html
ls -ldZ /web
man semanage-fcontext;
semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
restorecon -R -v /web
systemclt restart httpd;
curl localhost;
```
