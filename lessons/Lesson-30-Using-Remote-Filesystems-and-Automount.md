# Lesson 30: Using Remote Filesystems and Automount

- [Lesson 30: Using Remote Filesystems and Automount](#lesson-30-using-remote-filesystems-and-automount)
    - [30.1 Configuring a Base NFS Server](#301-configuring-a-base-nfs-server)
    - [30.2 Mounting NFS Shares](#302-mounting-nfs-shares)
      - [Mounting NFS Shares](#mounting-nfs-shares)
    - [30.3 Understanding Automount](#303-understanding-automount)
    - [30.4 Configuring Automount](#304-configuring-automount)
    - [30.5 Setting up Automount for Home Directories](#305-setting-up-automount-for-home-directories)
      - [Understandig Wildcard Mounts](#understandig-wildcard-mounts)
    - [Lesson 30 Lab: Setting up Automount](#lesson-30-lab-setting-up-automount)
    - [Lesson 30 Lab Solution: Setting up Automount](#lesson-30-lab-solution-setting-up-automount)

### 30.1 Configuring a Base NFS Server

```bash
dnf install nfs-utils;
mkdir -p /nfsdata /home/ldap/ldapuser{1..9}
echo "/nfsdata *(rw,no_root_squash)" >> /etc/exports;
echo "/home/ldap *(rw,no_root_squash)" >> /etc/exports
systemctl enable --now nfs-server;
systemctl enable --now firewalld;
for i in nfs mountd rpc-bind; do firewall-cmd --add-service $i --permanent; done
firewall-cmd --reload;
firewall-cmd --list-all;

# Run it on the nfs client machine.
showmount -e nfsserver;
```

### 30.2 Mounting NFS Shares

#### Mounting NFS Shares

- Make sure that **nfs-utils** is install.
- Use **showmount -e nfsserver** to show exports.
- Use **mount nfsserver:/share /mnt** to mount.

```bash
showmount -e nfsserver;
mount nfsserver:/nfsdata /mnt;
mount;
```

### 30.3 Understanding Automount

- In **/etc/auto.master** you'll identify the directory that automount should manage, and the file that is used for additional mount information.
  - **/data /etc/auto.nfsdata**
- In **/etc/auto.nfsdata** you'll identify the subdirectory on which to mount, and what to mount exactly.
  - files   -rw   nfsserver:/nfsdata
- Ensure the autofs service is started.
  - **systemclt enable --now autofs**
- Tip: check /etc/auto.misc for syntax examples on the exam. 

### 30.4 Configuring Automount

```bash
dnf install -y autofs;
cd /etc/
vim auto.master;
/nfsdata  /etc/auto.nfsdata

Save and close the file.

vim auto.nfsserver;
files   -rw  nfsserver:/nfsdata
Save and close the file.

systemctl enable --now autofs;
systemctl start autofs;
ls /etc/
cd /etc/nfsdata/
ls -al;
cd files;
mount | tail -3;
```

### 30.5 Setting up Automount for Home Directories

#### Understandig Wildcard Mounts

- Automount is common for home directory access.
- In this scenario, an NFS server is providing access to homedirectories, and the homedirectory is automounted by a user while logging in.
- To support different directory names in one automount line, wildcards are used.
- ***   -rw   nfsserver:/home/ldap&**

```bash
# Do it in nfs client.
showmount -e nfsserver;
vim /etc/auto.master
/home   /etc/auto.homes
Save and close the file.

vim /etc/auto.mohes
*   -rw   nfsserver:/home/ldap&
Save and close the file.

systemctl restart autofs;
cd /homes/
cd ldapuser1
cd ..
cd ldapuser3
```

### Lesson 30 Lab: Setting up Automount

- To perform this lab it is recommended to use a second server with the name **nfsserver**. If that's not possible, configure the NFS server parts on localhost.
- On **nfsserver**, create home directories /home/ldap/ldapuser1..9
- Configure **nfsserver** to NFS export these home directories.
- Automount the home directories on /homes/. The result should be that /homes/ldapusern is accessed, the corresponding directory from **nfsserver** is mounted.

### Lesson 30 Lab Solution: Setting up Automount

```bash
ssh student@nfsserver;

mkdir -p /home/ldap/ldapuser{1..9}
ls /home/ldap/
dnf install -y nfs-utils;

vim /etc/exports
/home/ldap  *(rw,no_root_squash)

Save and close the file.
systemctl enable --now nfs-server;
showmount -e localhost;
systemctl enable --now firewalld;
systemclt start firewalld;

for i in nfs mountd rpc-bind; do firewall-cmd --add-service $i --permanent; done
firewall-cmd --reload;

# NFS Client.
sudo -i;
hostnamectl set-hostname nfsserver;
exit;
sudo -i;
vim /etc/hosts
192.168.1.10     nfsserver.example.com    nfsserver
showmount -e nfsserver;

dnf install -y autofs;
vim /etc/auto.master;
/homes /etc/auto.homes

vim /etc/auto.homes
*     -rw   nfsserver:/home/ldap/&

systemctl restart autofs;
cd /homes/ldapuser5/
```
