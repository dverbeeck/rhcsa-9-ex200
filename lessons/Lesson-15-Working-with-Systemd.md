# Lesson 15: Working with Systemd

- [Lesson 15: Working with Systemd](#lesson-15-working-with-systemd)
    - [15.1 Exploring Systemd Units](#151-exploring-systemd-units)
      - [Understandig Systemd Units](#understandig-systemd-units)
      - [Understandig Unit Types](#understandig-unit-types)
      - [Understandig Service Units](#understandig-service-units)
    - [15.2 Managing Systemd Services](#152-managing-systemd-services)
      - [Systemd Unit Management Tasks](#systemd-unit-management-tasks)
    - [15.3 Modifying Systemd Service Configuration](#153-modifying-systemd-service-configuration)
      - [Modifying Systemd Unit Configuration](#modifying-systemd-unit-configuration)
    - [15.4 Managing Unit Dependencies](#154-managing-unit-dependencies)
      - [Understandig Unit Dependencies](#understandig-unit-dependencies)
    - [15.5 Masking Services](#155-masking-services)
      - [Understandig systemctl mask](#understandig-systemctl-mask)
    - [Lesson 15 Lab: Managing Systemd Units](#lesson-15-lab-managing-systemd-units)
    - [Lesson 15 Lab Solution: Managing Systemd Units](#lesson-15-lab-solution-managing-systemd-units)

### 15.1 Exploring Systemd Units

#### Understandig Systemd Units

- Systemd is started a the first process after loading the kernel and is the manager of everything.
- It is used for starting services like the secure shell server, a web server.
- Apart from services, systemd takes care of many more items.
- The items started by systemd are referred to as units.
- `systemctl` is the main management tool for systemd.
- To get an overview of all types of units that are available, use `systemctl -t help` 

#### Understandig Unit Types

- Service units are used to start processes.
- Socket units monitor activity on a prt and start the corresponding Service unit when needed.
- Timer units are used to start service periodically.
- Path units can start Service units when activity is detected in the file system.
- Mount units are used to mount file systems.
- Other unit types are available, though less relevant for RHCSA.

#### Understandig Service Units

- Service units are often used to start daemon processes.
- Other types of Service units are available as well.
  - `Type=oneshot` can be used to start any command through systemd.

```bash
systemctl
systemctl -t help
systemctl list-units
systemctl list-units -t timer
systemctl list-unit-files
```

### 15.2 Managing Systemd Services

#### Systemd Unit Management Tasks

- `systemctl status` shows the current status of any unit
  - `systemctl status sshd`
- The `Active`: line in the output shows the current status.
- The `Loaded`: line in the output shows which configuration is loaded, ans whether the unit is enabled for autmatic starting.

```bash
systemctl status sshd
```

- Use `systemctl status` to get current status.
- `systemctl start` will start a unit that is not currently active.
- `systemctl stop` will stop the unit.
- `systemctl enable --now` is used to flag the unit for automatic starting upon system start.
- `systemctl disable --now` is used to flag the unit to be no longer automatically started.
- `systemctl reload` will reload the unit congiguration without restarting the unit.
- `systemctl restart` restarts the unit after which the process it manages gets a new PID.
```bash
systemctl stop sshd
systemctl status sshd
systemctl disable sshd
systemctl enable --now sshd
```


### 15.3 Modifying Systemd Service Configuration

#### Modifying Systemd Unit Configuration

- Default system-provided systemd unit files are in `/usr/lib/systemd/system`
- Custom unit files are in /etc/systemd/system
- Run-time automatically-generated unit files are in /run/systemd
- While modifying a unti file, do NOT edit the file in /usr/lib/systemd/system but create a custom file in /etc/systemd/system that is used as an overlay file.
- Better: use `systemctl edit unit.service` to edit unit files.
- Use `systemctl show` to show available parameters
- Using `systemctl reload` may be required.

```bash
systemctl cat httpd.service
dnf install -y httpd
systemctl cat httpd.service
systemctl show httpd.service
systemctl edit httpd.service
export EDITOR=/usr/bin/vim # Set default editor as vim.
systemctl edit httpd.service

[Service]
Restart=always
RestartSec=5s

Save and close the file
systemctl cat httpd.service
systemctl daemon-reload
systemctl restart httpd.service
ps aux | grep http
kill -9 <pid>
systemctl status httpd.service
systemctl status httpd.service
```

### 15.4 Managing Unit Dependencies

#### Understandig Unit Dependencies

- Systemd units normally depend on other units.
- Use `systemctl list-dependencies` for a complete overview of all currently loaded units and their dependencies.
- Use `systemctl list-dependencies UNIT` to see depndencies for any unit.
 
```bash
systemctl list-dependencies
systemctl list-dependencies sshd.servie
```

### 15.5 Masking Services

#### Understandig systemctl mask

- Some units cannot work simultaneously on the same system.
- To prevent administrators from accidentally starting these units, use `systemctl mask`
- `systemctl mask` links a unit to the /dev/null device, which ensures that it cannot be started.
- `systemctl unmask` removes the unit mask.

```bash
systemctl stop httpd
systemctl mask httpd
systemctl start httpd
systemctl unmask httpd
systemctl start httpd
```

### Lesson 15 Lab: Managing Systemd Units

- Make sure the httpd service is automatically started.
- Edit its configuration such that on failure, it will continue after 1 minute.


### Lesson 15 Lab Solution: Managing Systemd Units

```bash
systemctl edit httpd.service

[Service]
Restart=always
RestartSec=60s

Save and close the file

systemctl restart httpd.service
systemctl enable httpd
killall httpd
systemctl status httpd
```
