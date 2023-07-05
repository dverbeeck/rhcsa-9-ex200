# Lesson 28: Running Automatic Installations

- [Lesson 28: Running Automatic Installations](#lesson-28-running-automatic-installations)
    - [28.1 Exploring Solutions for Automatic Installations](#281-exploring-solutions-for-automatic-installations)
      - [Understandig Automatic Installations](#understandig-automatic-installations)
    - [28.2 Creating a Kickstart File](#282-creating-a-kickstart-file)
      - [Creating a Kickstart File](#creating-a-kickstart-file)
      - [Using Common Kickstart File Options](#using-common-kickstart-file-options)
    - [28.3 Using a Kickstart File for Automatic Installations](#283-using-a-kickstart-file-for-automatic-installations)
      - [Using a Kickstart File](#using-a-kickstart-file)
    - [28.4 Installing Virtual Machines](#284-installing-virtual-machines)
      - [Understandig RHEL-based Virtualization](#understandig-rhel-based-virtualization)
      - [Configuring RHEL as Virtualization Host](#configuring-rhel-as-virtualization-host)
      - [Installing Virtual Machines](#installing-virtual-machines)
    - [Lesson 28 Lab: Configuring a Kickstart File](#lesson-28-lab-configuring-a-kickstart-file)
    - [Lesson 28 Lab Solution: Configuring a Kickstart File](#lesson-28-lab-solution-configuring-a-kickstart-file)

### 28.1 Exploring Solutions for Automatic Installations

#### Understandig Automatic Installations
Several solutions exist for performing automated installations.
- Vagrant is used for autmatic deployment of virtual machines.
- Cloud-init and other templates can be used in cloud environments.
- Kickstart can be used with a PXE-boot server to provide instructions for automatic installation of RHEL.
  - A Kickstart file contains all installation instructions to set up a RHEL instance. It can be used to easily reproduce installations.

### 28.2 Creating a Kickstart File

#### Creating a Kickstart File

- After installation, a file anaconda-ks.cfg is created and saved in the root user home directory.
- Edit this file manually to make any changes that are required.
- After editing the Kickstart file, use **ksvalidator /root/anaconda-ks.cfg** to verify file syntax.

#### Using Common Kickstart File Options

- **url --url="http://myserver/..."** specifies which URL to access for the installation media.
- **repo --name="myrepo" --baseurl=...** how to access repositories.
- **text** forces text mode installation.
- **vnc --password=password** enables the VNC viewer for remote access to the installation.
- **clearpart --all --drive=sda,sdb** removes all partitions.
- **part /home --fstype-ext4 --label=home --size=2048 --maxsize=4096 --grow** creates and mounts a partition.
- **autopart** automatically creates root, swap and boot partition.
- **network --device=ens33 --bootproto=dhcp** configures the primary network interface.
- **firewall --enabled --service=ssh,http** opens the firewall.
- **timesource --ntp-server pool.ntp.org** sets up NTP.
- **rootpw --plaintext secret** configures plain text root password.
- **selinux --enforcing** activates SELinux.

```bash
cd /root;
cat anaconda-ks.cfg;
```

### 28.3 Using a Kickstart File for Automatic Installations

#### Using a Kickstart File

- Typically, the kickstart file is provided on an installation server.
- Before starting the installation, the client indicates where to get the Kickstart file from.
  - Use **ks=http://lab.com/ks.cfg** to access from a web server.
  - Use **ks=hd:LABEL=MYLABEL:/directory/file** to access from a device (such as USB) - this works with LABEL = or UUID= only.
  - Or use the interface provided by the installation program (as in Virtual Machine Manager).

### 28.4 Installing Virtual Machines

#### Understandig RHEL-based Virtualization

- Virtual machines can be used to run independent operating systems on top of a host machine.
- The Linux kernel offers KVM (Kernel-based Virtual Machine) as the default virtualization solution.
- Red Hat Enterprise Linux can be used as a virtualization host, using KVM, or as a virtualization client.

#### Configuring RHEL as Virtualization Host

- The host platform must offer virtualization extensions: **grep -E "svm|vmx" /proc/cpuinfo**
- Install the virtualization package group: **dnf group install "Virtualization Host"**
- Validate that the host meets all conditions: **virt-host-validate**

#### Installing Virtual Machines

- From the command line.
  - **dnf install virt-install**
  - **virt-install --name testvm --memory 2048 --vcpus 2 --disk size 20 --os-type linux --cdrom /rhel9.iso**
- Using Web console
  - dnf install cockpit-machines;
  - systemclt enable --now cockpit.socket;
  - Log in at https://localhost:9090 
    - un: root
    - pw: root-uesr-pw
  - From administrative mode, select Virtual Machines > Create VM
- Notice that recent versions of MacOS don't support nested virtualization very well and you'll get a failure message.

### Lesson 28 Lab: Configuring a Kickstart File

- Create a kickstart file with the name my-ks.cfg and make sure it can be used for an automated installation, and meets the following requirements.
  - The installer prompts for a password.
  - Network connectivity will be enabled on boot.
  - The local machine name is set to server10.example.com
 
### Lesson 28 Lab Solution: Configuring a Kickstart File

```bash
cp anaconda-ks.cfg my-ks.cfg;
vim my-ks.cfg;
--hostname=server10.example.com # Append line starts with network
remove root passwor line to enter manually while installing.
dnf provides */ksvalidator
dnf install -y pykickstart;
ksvalidator my-ks.cfg;
```
