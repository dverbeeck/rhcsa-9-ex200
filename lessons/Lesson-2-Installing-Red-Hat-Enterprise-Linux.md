# Lesson 2: Installing Red Hat Enterprise Linux

- [Lesson 2: Installing Red Hat Enterprise Linux](#lesson-2-installing-red-hat-enterprise-linux)
    - [2.1 Exploring Server Requirements](#21-exploring-server-requirements)
      - [Server Requirements](#server-requirements)
    - [2.2 Performing a Basic Installation](#22-performing-a-basic-installation)
    - [2.3 Installing with Custom Partitioning](#23-installing-with-custom-partitioning)
      - [Using Custom Partitioning](#using-custom-partitioning)
    - [2.4 Logging into the Server](#24-logging-into-the-server)
      - [Logging in](#logging-in)
    - [2.5 Deploying RHEL in Cloud](#25-deploying-rhel-in-cloud)
      - [Using RHEL in Cloud](#using-rhel-in-cloud)
    - [Lesson 2 Lab: Installing Red Hat Enterprise Linux](#lesson-2-lab-installing-red-hat-enterprise-linux)
    - [Lesson 2 Lab Solution: Installing Red Hat Enterprise Linux](#lesson-2-lab-solution-installing-red-hat-enterprise-linux)

### 2.1 Exploring Server Requirements

#### Server Requirements

- Requirements depend on the type of installation.
  - Physical, cloud or virtual.
  - With or without GUI.
  - What you are going to do with the server.
- Requirements for installing a virtual machine in this class.
  - 2GiB or RAM
  - 20GiB Disk space
  - Network connection
  - Optical drive or access to DVD ISO

### 2.2 Performing a Basic Installation

- Download and Install - Oracle VM VirtualBox.
- Install Red Hat Enterprise Linux.

### 2.3 Installing with Custom Partitioning

#### Using Custom Partitioning

- Linux serves typically use multiple storage volumes.
  - Partitions are the base solution for offering multiple separated storage areas.
  - Logical volumes can be used as an alternative for partitions and are discussed in Lesson 19.
- A small partition containing all that is required for booting.
- A root partition containing operating system essential files.
- Other file types that are often organized on dedicated partitions.
  - Log files.
  - User home directories.
  - Server document roots.
  - Container images and more.

### 2.4 Logging into the Server

#### Logging in

- To work on Linux, you'll need to identify yourself by logging in using a user account.
- A user account is created when the server installs.
  - Some servers have a root user account with unlimited access privileges.
  - Some servers only have a regular user account that can perform administrative tasks using **sudo**
  - Some servers have both.
- Avoid logging in as root, because it has a higher risk of security-related problems.

### 2.5 Deploying RHEL in Cloud

#### Using RHEL in Cloud

- Using RHEL in cloud is different.
  - In cloud, RHEL is deployed, not installed.
  - The cloud provies the boot procedure, not Linux.
  - Different procedures are used by different cloud providers.
- In this class, using cloud instances is not recommended.

### Lesson 2 Lab: Installing Red Hat Enterprise Linux

Install a RHEL Server that meets the following requirements.
- Make sure a **graphical user interface** is installed.
- Configure a **10GiB Root partition**.
- Use a **1GiB Swap partition**.
- Make sure that at least **4GiB** of disk space remains as unused.
- Set the root password to **password**
- Create a user **student** with the password **password**
- Configure the network interface to contact a **DHCP Server**.

### Lesson 2 Lab Solution: Installing Red Hat Enterprise Linux

- RHEL Installation.
