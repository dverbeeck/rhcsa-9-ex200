# Lesson 22: Troubleshooting RHEL

- [Lesson 22: Troubleshooting RHEL](#lesson-22-troubleshooting-rhel)
    - [22.1 Using Troubleshooting Modes](#221-using-troubleshooting-modes)
    - [22.2 Changing the Root Password](#222-changing-the-root-password)
    - [22.3 Using the Boot Debug Shell](#223-using-the-boot-debug-shell)
      - [Understandig the Early Boot Debug Shell](#understandig-the-early-boot-debug-shell)
      - [Demo: Using the Early Boot Debug Shell](#demo-using-the-early-boot-debug-shell)
    - [22.4 Troubleshooting Filesystem Issues](#224-troubleshooting-filesystem-issues)
      - [Preventing /etc/fstab Related Issues](#preventing-etcfstab-related-issues)
    - [22.5 Fixing Network Issues](#225-fixing-network-issues)
      - [Understandig Common Network Issues](#understandig-common-network-issues)
      - [Demo: Verifying Network Configuration](#demo-verifying-network-configuration)
    - [22.6 Managing Performance Issues](#226-managing-performance-issues)
    - [22.7 Troubleshooting Software Issues](#227-troubleshooting-software-issues)
    - [22.8 Fixing Memory Shortage](#228-fixing-memory-shortage)
    - [22.9 Consulting Red Hat Websites for Tips](#229-consulting-red-hat-websites-for-tips)
    - [Lesson 22 Lab: Troubleshooting the Root Password](#lesson-22-lab-troubleshooting-the-root-password)
    - [Lesson 22 Lab Solution: Troubleshooting the Root Password](#lesson-22-lab-solution-troubleshooting-the-root-password)

### 22.1 Using Troubleshooting Modes

- Explaination


### 22.2 Changing the Root Password

- Enter Grub menu while booting.
- Find the line that loads the Linux kernel and add `init=/bin/bash` to the end of the line.
- mount -o remount,rw /
- passwd root
- touch /.autorelabel
- exec /usr/lib/systemd/systemd

### 22.3 Using the Boot Debug Shell

#### Understandig the Early Boot Debug Shell
- If something goes wrong early in the boot procedure, it may be userful to have an option to open a debug shell.
- This is why the `debug-shell.service` is provided.
- If this service is active, it starts a root shell that can be accessed on TTY9 without having to enter a root password.
- Obviously this is useful for troubleshooting and analyzing, but once no loger needed, it should immediately be removed.

#### Demo: Using the Early Boot Debug Shell

- `systemctl enable --now debug-shell.service`
- Mess up /etc/fstab file to make sure the system get into error.
- reboot
- While booting access a virtual terminal on TTY9 
  - Ctrl-Alt-F9
  - (Mac: Cmd-Opt-Fn-F9)
- Correct the content in /etc/fstab
- reboot
- Enter a root shell without entering a password
- `systemclt disable --now debug-shell.service` 

### 22.4 Troubleshooting Filesystem Issues

- Real corruption does occur, but not often, and is automatically fixed.
- Problems occur when making typo's in /etc/fstab
- To fix: if necessary, remount filesystem in read/write state and eit /etc/fstab
- Fragmentation can be an issue, different tools exist to fix.
  - `xfs_fsr` is the XFS File System Reorganizer, it optimizes XFS file systems.
  - `e4defrag` can be used to defragment Ext4.

#### Preventing /etc/fstab Related Issues

- Issues often occur after modifying /etc/fstab
- To prevent having issues, do the following after making modifications
  - Use `mount -a` to mount all filesystems in /etc/fstab that haven't been mounted yet.
  - Use `findmnt --verify` to verify syntax
- To verify that all works well, use `reboot` after making changes to /etc/fstab

### 22.5 Fixing Network Issues

#### Understandig Common Network Issues
- **Wrong subnet mask**: all nodes in the same network should be in the same subnet.
- **Wrong router**: the router must always be in the local network 
- **DNS not working**: verify /etc/resolv.conf contents.
#### Demo: Verifying Network Configuration

```bash
ip addr show;
ip route show;
ping 8.8.8.8;
cat /etc/resolv.conf;
ping google.com;
dig google.com;
```

### 22.6 Managing Performance Issues

- troubleshooting performance is an art on its own.
- Focus on the four key areas of performance.
  - memory
  - CPU load
  - disk load
  - network
- Use `top` to get a generic image, and more specialized tools only if you have strong indication of what is wrong.

```bash
while true; do true; done &
while true; do true; done &
top;
press `k` # to kill process
```

### 22.7 Troubleshooting Software Issues

- Dependency problems in RPM's.
  - Should not occur when using repositories.
- Library problmes.
  - Run `ldconfig` to update the library cache.
- Use containers to avoid seoftware dependency version issues.

```bash
rpm -ivh ansible-freeipa-xxx.rpm;
ldconfig;
```

### 22.8 Fixing Memory Shortage

- Memory issues appear if available memory as shown by **free** is low.
  - The definition of low depends on server workload.
- As a first help to fix memory issues, swap space can be used.
- Use `vmstat 2 25` to make sure that adding swap space donen't lead to too much I/O traffic. 

```bash
reboot;
Update the grub boot menu as `MEM=768M`
Ctrl+Alt+1 # Login from text console.
top;
vmstat 2 25;
```

### 22.9 Consulting Red Hat Websites for Tips

- access.redhat.com

### Lesson 22 Lab: Troubleshooting the Root Password

- Let's assume you have lost the root user password. Reset it.

### Lesson 22 Lab Solution: Troubleshooting the Root Password

- Enter into the Grub menu while booting.
- Find the line that loads the Linux kernel and add `init=/bin/bash` to the end of the line.
- Press Ctrl+x
- mount -o remount,rw /
- touch aaa # Test write permission.
- passwd root
- touch /.autorelabel
- exec /usr/lib/systemd/systemd
