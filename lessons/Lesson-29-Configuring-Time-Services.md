# Lesson 29: Configuring Time Services

- [Lesson 29: Configuring Time Services](#lesson-29-configuring-time-services)
    - [29.1 Exploring Linux Time](#291-exploring-linux-time)
      - [Managing Linux Time](#managing-linux-time)
    - [29.2 Setting Time with timedatectl](#292-setting-time-with-timedatectl)
      - [Using timedatectl](#using-timedatectl)
    - [29.3 Managing an NTP Client](#293-managing-an-ntp-client)
    - [Lesson 29 Lab: Configuring Time Services](#lesson-29-lab-configuring-time-services)
    - [Lesson 29 Lab Solution: Configuring Time Services](#lesson-29-lab-solution-configuring-time-services)

### 29.1 Exploring Linux Time

- While booting, the system gets its time from the hardware clock.
- System time is set next, according to the hardware clock.
- Internet time can be used to sysnchronize time.

#### Managing Linux Time

- **hwclock** is used to set hardware time.
- Also use it to synchronize time.
  - **hwclock --systohc**
  - **hwclock --hctosys**
- **date** is used to show and set time.
- **timedatectl** is used to manage time and time zone configuration.

```bash
date;
hwclock;
hwclock --hctosys;
date;
```

### 29.2 Setting Time with timedatectl

#### Using timedatectl

- **timedatectl** is a new utility that allows you to manage all aspects of system time.
  - **timedateclt status** will show all time properties currently used.
  - **timedateclt set-time** is used to change the time.
  - **timedateclt set-timezone** is used to change the timezone.
  - **timedateclt set-ntp** enables or disables NTP time synchronization.
- To synchronize time using NTP, an NTP service must be configured.
  - RHEL 9 uses **chrony**
  - systemd-timesyncd.service is another NTP service - not currently used on REHL.
  - 
```bash
time;
timedateclt status;
timedateclt list-timezones;
timedateclt set-timezone <Timezone>
timedateclt status;
```

### 29.3 Managing an NTP Client

- **chronyd** is the default RHEL 9 NTP service.
- Use /etc/chrony.conf to specify synchronization parameters.
  - **pool 2.rhel.pool.ntp.org iburst** configures a pool of NTP servers.
  - **server myserver.example.com** configures a single NTP time source.
  - Use **iburst** to permit fast synchronization.
- After modifying its contents, use **systemctl restart chronyd** to restart the **chronyd** service.
- Use **chronyc sources** to verify proper synchronization.

```bash
vim /etc/chrony.conf;
change the pool.
systemctl restart chronyd;
chronyc sources;
```

### Lesson 29 Lab: Configuring Time Services

- Configure your system to synchronize time with the servers in pool.ntp.org

### Lesson 29 Lab Solution: Configuring Time Services

```bash
timedatectl status;
vim /etc/chrony.conf;
Chagne the pool.
systemclt restart chronyd;
chronyc sources;
```
