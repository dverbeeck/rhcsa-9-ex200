# Lesson 11: Managing Network Configuration

- [Lesson 11: Managing Network Configuration](#lesson-11-managing-network-configuration)
    - [11.1 Understanding IPv4 Networking](#111-understanding-ipv4-networking)
    - [11.2 Exploring IPv6 Networking](#112-exploring-ipv6-networking)
      - [IPv6 Address Assignment](#ipv6-address-assignment)
    - [11.3 Understanding NIC Naming](#113-understanding-nic-naming)
      - [Device Names](#device-names)
      - [BIOS Device Names](#bios-device-names)
    - [11.4 Defining Host Names and Host Name Resolution](#114-defining-host-names-and-host-name-resolution)
      - [Host Name Resolution](#host-name-resolution)
    - [11.5 Analyzing Network Configuration](#115-analyzing-network-configuration)
      - [The ip Command](#the-ip-command)
    - [11.6 Understanding NetworkManager](#116-understanding-networkmanager)
      - [NetworkManger](#networkmanger)
      - [Connection and Devices](#connection-and-devices)
      - [NetworkManager Permissions](#networkmanager-permissions)
    - [11.7 Managing Persistent Network Configuration with nmcli](#117-managing-persistent-network-configuration-with-nmcli)
      - [nmcli](#nmcli)
      - [nmcli con](#nmcli-con)
      - [ipv4.method](#ipv4method)
    - [11.8 Managing Persistent Network Configuration with nmtui](#118-managing-persistent-network-configuration-with-nmtui)
    - [11.9 Troubleshooting Networking](#119-troubleshooting-networking)
      - [Verify Connectivity](#verify-connectivity)
      - [Troubleshooting Routing](#troubleshooting-routing)
    - [Lesson 11 Lab: Managing Network Configuration](#lesson-11-lab-managing-network-configuration)
    - [Lesson 11 Lab Solution: Managing Network Configuration](#lesson-11-lab-solution-managing-network-configuration)

### 11.1 Understanding IPv4 Networking

- In IPv4, each node needs its own IP address, written in dotted decimal notation (192.168.1.100/24)
- Each IP address must be indicated with the subnet mask behind it.
- The default router or gatewayspecifies which server to forward packets to that have an external destination.
- The DNS nameserver is the IP address of a server that helps to resolve names to Ip addresses and the other way around.
- IPv4 is till the most common IP version, but IPv6 addresses can be used as well.
- IPv6 addresses are written in hexadecimal notation (fd01::8eba:210)
- IPv4 and IPv6 can co-exist on the same network interface.

### 11.2 Exploring IPv6 Networking

- IPv6 was introduced in the 1990's to overcome the Shortage of world-wide unique IPv4 addresses.
- IPv6 is used extensively by Internet Service Providers to address the core internet infrastructure.
- End-uers and companies mostly use IPv4 behind a NAT router.
- Red Hat Enterprise Linux offers dual stack IPv4 and IPv6.
- IPv4 addresses are 128-bit numbers, which are normally expressed as 8 colon-separated groups of four hexadecimal numbers.
- In these numbers, leading zero's re omitted
  - IPv6 ip address goes here.
- Long strings of zeros can be replaced by one block of two colon characters.
  - ::210
- When combining as IPv6 address with a port number, enclose the IPv6 address in square brackets
  - [ipv6-ip-address]:80
- IPv6 addresses have a standard subnet of 64 bits.
- Typically, network providers hand out a /48 prefixed address, which leaves 16 bits to the customer to address subnets.
- Different IPv6 address types are available.

| Purpose | Example | Description |
| :------- | :------- | :----------- |
| localhost | ::1/128 | Localhost address, used on loopback interface
| unspecified | :: | Used to refer to all IP addresses
| default route | ::/0 | Used in default router specification
| global unicast | 2000::/3 | IPv6 addresses currently being allocated
| unique local | fd00::/8 | Addresses for internal use like 192.168.0.0
| link local | fe80::/10 | Non-routable auto assigned for internal use
| multicast | ff00::/8 | Multicast addresses

#### IPv6 Address Assignment

- Apart from manual allocation and DHCP, IPv6 supports Stateless Address Autoconfiguration (SLAAC)
- In SLAAC, a host sents a router solicitation to the ff02::2 multicast group to accessa all routers.
- A router answers tht request, sending all relevant information
- The host adds an automatically generated host ID to the network prefix to obtain a unique address in this way.
- To enable RHEK 9 for SLAAC support, install the `radvd` package

### 11.3 Understanding NIC Naming

#### Device Names

- IP address configuration needs to be connected to a specific network device
- Use `ip link show` to see current devices, and `ip addr show` to check their configuration
- Every system has an `lo` device, which is for internal Networking
- Apart from that, you'll see the name of the real network device, which is presented as a BIOS name.

#### BIOS Device Names

- Classical naming is using device names like eth0, eht1 and so on
  - These device names don't reveal any information about physical device location
- BIOS naming is based on hardware properties to give more specific information in the deivce name.
  - em[1-N] for embedded NICs
  - eno[nn] for embedded NICs
  - p<slot>p<port> for NICs on the PCI bus.
- If the driver doesn't reveal network device properties, classical naming is used.

```bash
ip link show
ip addr
```

### 11.4 Defining Host Names and Host Name Resolution

#### Host Name Resolution

- `hostnamectl set-hostname` is used to manage hostnames.
- The hostname is written to /etc/hostnames
- To resolve hostnames /etc/hosts is used
  - 10.0.0.11 server2.example.com   server2
- /etc/resolv.conf contains DNS client configuration
- The order of host name resolution is determined through /etc/nsswitch.conf

```bash
hostname
hostnamectl --help
hostnameclt hostname server1.example.com
hostname
ip a 
vim /etc/hosts
192.168.1.20  server1.example.com server1
```

### 11.5 Analyzing Network Configuration

#### The ip Command
- The ip tool can be used to manage all aspects of IP networking
- It replaces the legacy ifconfig tool, do NOT use ifconfig anymore
- Use ip addr to manage address properties.
- Use ip link to show link properties
 - ip -s link
- Use ip route to manage route properties
  - ip route show
  - ip route add default via 10.0.0.1
   
```bash
ip a
ip a a dev ens160 10.0.0.10/24
ip a
man ifconfig
ip -s link
ip route show
ip route add default 2.2.2.0/24 via 10.0.0.1 # Dummy route to test it.
ip route show
```

### 11.6 Understanding NetworkManager

#### NetworkManger

- NetworkManager is the systemd service that manages network configuration.
- Configuration is stored in files in /etc/NetworkManager/system-connections
  - Legacy files in /etc/sysconfig/network-scripts are still supported but deprecated.
- Different applications are available to interface with NetworkManager
  - `nmcli` is a powerful command line utility.
  - `nmtui` offers a convenient text user interface.
  - GNOME offers graphical tools also

#### Connection and Devices

- In NetworkManager, devices are network interfaces
- Connections are collections of configuration setttings for a device, stored in the configuration file in /etc/NetworkManager/system-connections
- Only one connection can be active for a device.

#### NetworkManager Permissions

- Permissions to modify settings in NetworkManager are applied through `dbus`
- Non-privileged users that are logged in on the sonsole can change network settings.
- Non-privileged users that are logged in through `ssh` cannot
- Use `nmcli general permissions` for an overview of current permissions that apply

```bash
systemctl status NetworkManger
nmcli general status
nmcli general permissions
su - anil
nmcli general permissions
ssh student@localhost
nmcli general permissions
```

### 11.7 Managing Persistent Network Configuration with nmcli

#### nmcli
- `nmcli` has awesome tab completion
- `nmcli con show` shows current connections
- `nmcli dev status` shows current network devices
- `nmcli con add con-name mynewconnection ifname ens33 ipv4.addresses 10.0.0.10/24 ipv4.gateway 10.0.0.1 ipv4.method manual type ethernet` will add a new connection
- `nmcli con up mynewconnection` will activate the new connection

```bash
nmcli connection show
nmcli dev status
nmcli connection add con-name mynewconnection ifname ens33 ipv4.addresses 192.168.1.33/24 ipv4.gateway 192.168.1.1 ipv4.method manual type ethernet 
nmcli con show
nmcli con up mynewconnection
nmcli con show
ip a
```

#### nmcli con

- `nmcli con show mynewconnection` shows all connection settings.
- `nmcli con mod` will modify connection settings: use tab completion!
- `nmcli con reload` will reload the modified connection

#### ipv4.method

- Use `ipv4.method manual` on connections that don't use DHCP
- Without this setting, a DHCP server will be contacted, even if static configuration has been set.

### 11.8 Managing Persistent Network Configuration with nmtui

```bash
nmtui # Use this in the exam.
```

### 11.9 Troubleshooting Networking

#### Verify Connectivity

- Use `ping` to verify connectivity
  - `ping -c 4 myserver` sends 4 packets and then stops.
  - `ping6 2001::210` uses ping6
- When using `ping6` on link-local addresses, you must include the NIC name in the command
  - `ping6 ff02::1%ens33`

#### Troubleshooting Routing

- `ip route` prints the routing table.
- `ip -6 route` shows the IPv6 routing table.
- `tracepath example.com` shows the entire networking path.
- `tracepath6 example.com` does the same for an IPv6 path (if existing)
- `ss` is used to analyze socket statistics.
  - ss -tu
  - ss -tuna
  - ss -tunap

```bash
ping 8.8.8.8
ip a
ip route
nmtui
```

### Lesson 11 Lab: Managing Network Configuration

- Set the hostname for your server to rhcsaserver.example.com
- Set your server to a fixed IP address that matches your current network configuration
- Also set a second IP address 10.0.0.10/24 on the same network interface
- Enable host name resolution for your local server hostname
- Reboot and verify your network is still working with the new settings.

### Lesson 11 Lab Solution: Managing Network Configuration

```bash
nutui
ip a
vim /etc/hosts
192.168.29.133  rhcsaserver.example.com rhcsa
reboot
sudo -i
ip a
```
