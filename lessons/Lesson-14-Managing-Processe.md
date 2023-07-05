# Lesson 14: Managing Processes

- [Lesson 14: Managing Processes](#lesson-14-managing-processes)
    - [14.1 Using Signals to Manage Process State](#141-using-signals-to-manage-process-state)
      - [Using Signals](#using-signals)
      - [Demo Killing a Zombie](#demo-killing-a-zombie)
    - [14.2 Managing Process Priority](#142-managing-process-priority)
      - [Understandig Priority Management](#understandig-priority-management)
      - [Understandig Cgroups](#understandig-cgroups)
      - [Managing nice](#managing-nice)
    - [14.3 Using tuned Profiles](#143-using-tuned-profiles)
      - [Understandig System Tuning](#understandig-system-tuning)
      - [Understandig tuned](#understandig-tuned)
      - [Creating Custom Profiles](#creating-custom-profiles)
      - [Demo: Using tuned](#demo-using-tuned)
    - [14.4 Managing User Sessions and Processes](#144-managing-user-sessions-and-processes)
      - [Using Common utilities](#using-common-utilities)
      - [Using loginctl](#using-loginctl)
    - [Lesson 14 Lab: Managing Processes](#lesson-14-lab-managing-processes)
    - [Lesson 14 Lab Solution: Managing Processes](#lesson-14-lab-solution-managing-processes)

### 14.1 Using Signals to Manage Process State

#### Using Signals
- A signal allows the operating system to interrupt a process from software and ask it to do something.
- Interrupts are comparable to signals, but are generated from hardware.
- A limited amount of signals can be used and is documented in man 7 signals.
- Not all signals work in all cases.
- The `kill` command is used to send signals to PID's.
- You can also use `k` from top.
- Different kill-like commands exist, like `pkill` and `killall`.

#### Demo Killing a Zombie

- Run `zombie` from - https://github.com/sandervanvugt/rhcsa/blob/master/zombie
- Use `ps aux | grep zombie` and note the PID of the child as well as the parent.
- Use `kill <childpid>`; it will fail.
- Use `kill -SIGCHLD <parentpid>; it will be ignored.
- Use `kill <parentpid>; the Zombie will get adopted and init will reap it after a few seconds.

```bash
top
man 7 signal
top # press k and send the pid.
kill <pid>
killall <pid>
killall <process name>

dnf install -y git;
git clone https://github.com/sandervanvugt/rhcsa
cd rhcsa
ls
./zombie &
ps aux | grep defunct
ps fax | less
kill -SIGTERM <pid>
```

### 14.2 Managing Process Priority

#### Understandig Priority Management

- Linux Cgroups provide a framework to appy resource restrictions to Linux systems.
- Cgroups can limit the amount of CPU cycles, available memory, and more.
- If processes are equal from a perspective of Cgroups, the Linux `nice` and `renice` commands can be used to manage priority.

#### Understandig Cgroups

- In Cgroups, the Linux system is divided in 3 slices.
  - System: all systemd processes.
  - User: all user processes.
  - Machine: Virtual machines and containers.
- Each slice has an equal CPU weight.
- That means that if one or more processes within a slice requestes a maximum amount of CPU cycles, each slice will get an equal amount of CPU shares.   
  - So 20 systemd processes together gets as much as one user process that claims full CPU usage!
- In systemd, the CPUWeight can be set on individual systemd units.

#### Managing nice

- If no specific Cgroups are defined, Linux `nice` and `renice` can be used to define CPU priority.
- To change priorities of non-realtime processes, the `nice` and `renice` commands can be used.
- Nice values range from -20 up to 19.
- Negative nice value indicates an increased priority, a positive nice value indicates decreased priority.
- Users can set their processes to  lower priority, to increase priorities you need root access.
- `nice -n 19 dd if=/dev/zero of=/dev/null`
- Priority is alwyas relative to other processes.

```bash
nice -n 10 dd if=/dev/zero of=/dev/null &
nice -n -10 dd if=/dev/zero of=/dev/null &
top
q
cd /sys/
ls
cd bus/
ls
cd cpu/
cd devices/
tree
ls cpu1/
cat cpu1/online
pwd
echo 0 > cpu1/online
killall dd
echo 1 > cpu1/online
```

### 14.3 Using tuned Profiles

#### Understandig System Tuning

- Kernel tunables are provided through the /proc/sys directory in the /proc pseudo file system.
- Different files in the proc/sys directory contain the current settings as its value.
- Change the current value by echoing a new value into the file:
  - `cat /proc/sys/vm/swappiness`
  - echo 40 > /proc/sys/vm/swappiness
- To make settings persistent, write them to a file in /etc/sysctl.conf
  - cat >> swappiness <<EOF
  vm.swappiness = 40
  EOF

```bash
cat /proc/sys/vm/swappiness
echo 40 > /proc/sys/vm/swappiness
sysctl -a | wc
sysctl vm.swappiness
cd /proc/sysctl.d/

cat >> swappiness.conf <<EOF
  vm.swappiness = 40
  EOF
```

#### Understandig tuned

- To make system tuning easier, `tuned` is provided.
- `tuned` is a systemd service that works with different profiles.
- `tuned-adm list` shows current profiles.
- `tuned-adm profile virtual-guest` sets another profile as default.
- Each profile contains a file with the name tuned.conf, that has a wide range of performance relates settings.
- The `reapply_sysctl = 1` parameter in /etc/tuned/tuned-main.conf ensures that, in case of conflict, the sysctl paramerer wins.

```bash
tuned-adm --help
dnf install -f tuned
tuned-adm list
tuned-adm profile virtual-guest
```

#### Creating Custom Profiles

- Custom `tuned` profiles are stored as directories in /etc/tuned
- Each profile should have a file tuned.conf, containing the requested performance settings.
- After creating the directory with the corresponding tuned.conf, it will automatically be picket up.

#### Demo: Using tuned

```bash
sudo dnf install -y tuned
systemctl enable -now tuned
tuned-adm list
echo vm.swappiness = 33 > /etc/sysctl.d/swappiness.conf
sysctl -p /etc/sysctl.d/swappiness.conf
sysctl -a | grep swappiness
mkdir /etc/tuned/myprofile

cat >> /etc/tuned/myprofile/tuned.conf <<EOF
  [sysctl]
  vm.swappiness = 66
  EOF

tuned-adm profile myprofile
tuned-adm profile
sysctl -a | grep swappiness
cat /etc/tuned/tuned-main.conf
```

### 14.4 Managing User Sessions and Processes

#### Using Common utilities

- Use `ps -u username` to show processes owned by a specific user.
- Use `pkill -u username` to remove processes owned by a specific user.

#### Using loginctl

- `loginctl` is a part of `systemd`, which manages users and sessions.
- One user can have multiple sessions open simultaneously
- `loginctl list-users` and `loginctl list-sessions` shows users and sessions.
- `loginctl user-status <UID>` shows a tree of processes currently opened by this user.
- `loginctl terminate-session` and `loginctl terminate-user` can be used to stop current sessions or users.

```bash
chvt 5
vim myfile
chvt2
w
loginctl list-users
loginctl list-sessions
loginctl user-status <UID>
loginctl terminate-user <username>
loginctl list-users
loginctl user-status <UID>
chvt 5
loginctl list-sessions
loginctl terminate-session <session-id>
loginctl list-sessions
```

### Lesson 14 Lab: Managing Processes

- Create a user linda and open a shell as this user.
- As linda, run two background processes `sleep 600`; one of them with the highest possible priority, the other one with the lowest possible priority.
- Use the most efficient way to terminate all current sessions fo r user linda.

### Lesson 14 Lab Solution: Managing Processes

```bash
su - linda
nice -n 10 sleep 600 &
sleep 600 &

pkill -u linda
w
```
