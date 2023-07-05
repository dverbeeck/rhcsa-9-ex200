# Lesson 21: Managing the Boot Procedure

- [Lesson 21: Managing the Boot Procedure](#lesson-21-managing-the-boot-procedure)
    - [21.1 Exploring the Boot Procedure](#211-exploring-the-boot-procedure)
    - [21.2 Modifying Grub2 Runtime Parameters](#212-modifying-grub2-runtime-parameters)
      - [Modifying Grup2 Runtime Parameters](#modifying-grup2-runtime-parameters)
    - [21.3 Changing Grub2 Persistent Parameters](#213-changing-grub2-persistent-parameters)
      - [Modifying Grub2 Persistent Paramerters](#modifying-grub2-persistent-paramerters)
    - [21.4 Managing Systemd Targets](#214-managing-systemd-targets)
      - [Understandig Systemd Targets](#understandig-systemd-targets)
    - [21.5 Setting the Default Systemd Target](#215-setting-the-default-systemd-target)
      - [Setting the Default Systemd Target](#setting-the-default-systemd-target)
    - [21.6 Booting into a Specific Target](#216-booting-into-a-specific-target)
    - [Lesson 21 Lab: Managing the Boot Procedure](#lesson-21-lab-managing-the-boot-procedure)
    - [Lesson 21 Lab Solution: Managing the Boot Procedure](#lesson-21-lab-solution-managing-the-boot-procedure)

### 21.1 Exploring the Boot Procedure

- Explain about Linux boot procedure.

### 21.2 Modifying Grub2 Runtime Parameters

#### Modifying Grup2 Runtime Parameters

- From the Grub2 boot menu, press `e` to edit runtime boot options to the end of the line that starts with `linux`
  - `systemd.unit-emergency.target`
  - `systemd.unit-rescue.target`
- Press `c` to enter the Grub2 command mode.
  - From command mode, type `help` for an overview of available options.

### 21.3 Changing Grub2 Persistent Parameters

#### Modifying Grub2 Persistent Paramerters

- To edit persistent Grub2 parameters, edit the configuration file in `/etc/default/grub`
- Ater writing changes, compile changes to grub.cfg
  - `grub2-mkconfig -o /boot/grub2/grub.cfg`
  - `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg`

```bash
lsblk;
/etc/default/grub;
Remove `rhgb quiet` at the end of the 5th link.
save and close the file.
grub2-mkconfig -o /boot/grub2/grub.cfg;
```

### 21.4 Managing Systemd Targets

#### Understandig Systemd Targets

- A systemd target is a group of unit files.
- Some targets are isolatable, which means that they define the final state a system is starting in.
  - emergency.target
  - rescue.target
  - multi-user.target
  - graphical.target
- When enabling a unit, it is added to a specific target.

```bash
systemctl list-dependencies;
systemctl cat sshd.service;
```

### 21.5 Setting the Default Systemd Target

#### Setting the Default Systemd Target

- Use `systemctl get-default` to see the current default target.
- Use `systemctl set-default` to set a new default target.

```bash
systemctl get-default;
systemctl set-default multi-user.target;
systemctl list-dependencies;
reboot;
systemctl set-default graphical.target;
sudo reboot;
```

### 21.6 Booting into a Specific Target

- On the Grub2 boot prompt, use `systemctl.unit=xxx.target` to boot into a specific target.
- To change between targets on a running system, use `systemctl isolate xxx.target`

```bash
systemctl isolate multi-user.target;
login
sudo reboot;

# modify the grub line starts with linux
# remove `rhgb quiet` and enter 
systemd.unit=emergency.target
Ctrl+D # To start.
# Enter the root password
systemctl list-dependencies;
```

### Lesson 21 Lab: Managing the Boot Procedure

- Configure your system to boot in a multi-user target by default.
- Persistently remove the options that hide startup messages while booting.

### Lesson 21 Lab Solution: Managing the Boot Procedure

```bash
# First boot into emergency.target and then execute below commands.
touch hh;
mount -o remount,rw /;
ls /boot;
mount -a;
ls /boot;
systemctl set-default multi-user.target;
vim /etc/default/grub; # Remove rhgb quiet
grub2-mkconfig -o /boot/grub2/grub.cfg; # Reboot and then continue.
grub2-mkconfig -o /boot/grub2/grub.cfg;
systemctl set-default graphical.target;
reboot;
```
