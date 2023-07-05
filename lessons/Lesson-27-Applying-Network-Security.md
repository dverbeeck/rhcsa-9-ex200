# Lesson 27: Applying Network Security

- [Lesson 27: Applying Network Security](#lesson-27-applying-network-security)
    - [27.1 Analyzing Service Configuration with ss](#271-analyzing-service-configuration-with-ss)
      - [Monitoring Network Sockets with ss](#monitoring-network-sockets-with-ss)
    - [27.2 Managing RHEL Firewalling](#272-managing-rhel-firewalling)
      - [Understandig RHEL Firewalling](#understandig-rhel-firewalling)
    - [27.3 Exploring Firewalld Components](#273-exploring-firewalld-components)
      - [Understandig Firewalld Components](#understandig-firewalld-components)
    - [27.4 Configuring a Firewall with firewall-cmd](#274-configuring-a-firewall-with-firewall-cmd)
      - [Using firewall-cmd](#using-firewall-cmd)
      - [Demo: Allowing Incoming HTTP traffic](#demo-allowing-incoming-http-traffic)
    - [Lesson 27 Lab: Configuring a Firewall to Allow Service Access](#lesson-27-lab-configuring-a-firewall-to-allow-service-access)
    - [Lesson 27 Lab Solution: Configuring a Firewall to Allow Service Access](#lesson-27-lab-solution-configuring-a-firewall-to-allow-service-access)

### 27.1 Analyzing Service Configuration with ss

#### Monitoring Network Sockets with ss

- A network socket is a connection endpoint, consisting of an IP address followed by a port.
- Sockets also exist as UNIX sockets, which are endpointes in communication with services on Linux/UNIX.
- **ss** is the standard tool to shw socket information.
  - **ss** will show all connectins.
  - **ss -tu** shows connected TCP and UDP sockets.
  - **ss -tua** adds sockets that aer in listening state.
  - **ss -tln** shows TCP sockets that are in listening state only, without resolving host names.
  - **ss -tulpn** shows TCP and UDP sockets in listening state, and adds process name or PID to the output.

### 27.2 Managing RHEL Firewalling

#### Understandig RHEL Firewalling

- The Linux kernel provides the netfilter farmework to take care of firewall related network operations.
  - packet filtering.
  - network address translation.
  - port forwarding.
- Netfilter forwards specific operations to kernel modules.
- **netables** is the framework that applies firewalling.
- **firewalld** is a service, managed by systemd, which RHEL uses as the fornt end to manage **nftables** firewalls.

### 27.3 Exploring Firewalld Components

#### Understandig Firewalld Components

Firewalld is using different components to make firewalling easier.
- **Service**: the main component, contains one or more ports as well as optional kernel modules that should be loaded.
- **Zone**: a default configuration to which network cards can be assigned to apply specific settings.
- **Ports**: optional elements to allow access to specific ports.
- Additional components are available as well, but not frequently used in a base firewall configuration.
 
```bash
firewall-cmd --list-all;
```

### 27.4 Configuring a Firewall with firewall-cmd

#### Using firewall-cmd

- The **firewall-cmd** command is used to write firewall condiguration.
- Use the option **--permanent** to write to persistent (but not to runtime).
- Without **--permanent** the rule is written to runtime (but not to persistent).

#### Demo: Allowing Incoming HTTP traffic

```bash
systemctl status firewalld;
firewall-cmd --list-all;
firewall-cmd --get-services;
firewall-cmd --get-services | grep http
firewall-cmd --add-service http;
firewall-cmd --add-service http --permanent;
```

### Lesson 27 Lab: Configuring a Firewall to Allow Service Access

- Configure **firewalld** such that remote access to the SSH and FTP processes is allowed. Make sure the configuration is applied immediately as well as persistently.

### Lesson 27 Lab Solution: Configuring a Firewall to Allow Service Access

```bash
firewall-cmd --list-all;
firewall-cmd --get-services | grep ftp;
firewall-cmd --add-service ftp;
firewall-cmd --add-service ftp --permanent; 
firewall-cmd --list-all;
```
