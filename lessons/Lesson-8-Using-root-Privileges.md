# Lesson 8: Using root Privileges

- [Lesson 8: Using root Privileges](#lesson-8-using-root-privileges)
    - [8.1 Understanding the root User](#81-understanding-the-root-user)
    - [8.2 Switching User with su](#82-switching-user-with-su)
    - [8.3 Performing Administrator Tasks with sudo](#83-performing-administrator-tasks-with-sudo)
    - [8.4 Managing sudo Configuration](#84-managing-sudo-configuration)
      - [Providing Access to Specific Tasks](#providing-access-to-specific-tasks)
    - [8.5 Using ssh to Log In Remotely](#85-using-ssh-to-log-in-remotely)
    - [Lesson 8 Lab: Using root Privileges](#lesson-8-lab-using-root-privileges)
    - [Lesson 8 Lab Solution: Using root Privileges](#lesson-8-lab-solution-using-root-privileges)

### 8.1 Understanding the root User

- Since the earliest days of UNIX, a user account with UID 0 exists to perform administration tasks.
- On Linue, this user by default has the name root.
- The root user operates in kernel space and for that reason has unlimited access to all parts of the system.
- While installing RHEL 9, the root user can be activated or not
- If the root user is not activated, the administrative user is used for administration tasks
- To activate the root user, you will just have to set a password.
- From a security perspective, it may be a good idea not to have an active root user.
- Additional measures are possible to restrict root user access to a system.
  - Remote root login using secure shell (SSH) can be allowed or not.

### 8.2 Switching User with su

- The `su` command is used to switch current user account from a shell environment.
- If `su` is used with the `-` option, the complete environment of the target user is entered 
- If the root user has a password `su -` can be used
- Using `su -` to open a root shell is considered bad practive, use `sudo -i` instead.
- The `su` command can be useful for testing other user accounts

```bash
su - anil
Password:

whoami
exit
su - 
Password: # Enter root user password.
```

### 8.3 Performing Administrator Tasks with sudo

- `sudo` is a more secure mechanism to perform administration tasks
- Behind `sudo` is the /etc/sudoers configuration file
- While editing /etc/sudoers through `visudo`, very detailed administration privileges can bbe assigned
- To run an administration task using `sudo`, use `sudo command`
- This will prompt for the current user password, and run the command if this is allowed through /etc/sudoers
- To open a root shell, `sudo -i` can be used.

```bash
sudo ls /root
```

### 8.4 Managing sudo Configuration

- Sudo configuration is managed through /etc/sudoers
- Don't edit this file directly, only edit it through visudo
- Instead of editing /etc/sudoers, consider creatig drop-in file in /etc/sudoers.d
- /etc/sudoers is installed from packages and may be overwritten, drop-in file will never be overwritten
- Users tht are a member of the group `wheel` get full sudo access
  - This is accomplished by `%wheel ALL=(ALL) ALL` in /etc/sudoers
  - Use `usermod -aG wheel myuser`to add a user to the group wheel
- Do NOT enable the line `%wheel ALL=(ALL) NOPASSWD: ALL`
  - It will provide full sudo access without entering a password and is very dangerous
- If you don't like entering your user password every five minutes, increase authentication token expiration by adding the following.
  - Defaults timestamp_type=global,timestamp_timeout=60

#### Providing Access to Specific Tasks

- Use drop-in file to provide admin access to specific tasks
  - anil ALL=/sbin/userddd, /usr/bin/passwd
- Consider using command arguments to make the commands mor especific
  - %users ALL=/bin/mount /dev/sdb, /bin/umount /dev/sdb
  - anil ALL=/usr/bin/passwd,! /usr/bin/passwd root

```bash
sudo vim /etc/sudoers.d/anil

anil    ALL=/usr/bin/passwd, ! /user/bin/passwd root # Save and close the file
sudo useradd anil
sudo passwd anil

su - anil
sudo passwd john
```

### 8.5 Using ssh to Log In Remotely

- By default, all RHEL servers run a Secure Shell (SSH) server.
- Use `systemctl` to verify
  - `systemctl status sshd`
- SSH access is allowd through the firewall by default
- Notice that root access is often denied
- Use `ssh` to connect to a remote server
  - On the remote server, use `ip a` to find the IP address
  - `ssh 192.16### 8.1.10` will connect to this IP address using your current user account
  - ssh user@192.16### 8.1.10 will connect as a specific user

```bash
sudo systemctl status sshd
ip a
ssh 192.16### 8.1.10
yes
exit

ssh anil@192.16### 8.1.10
yes
exit
```

### Lesson 8 Lab: Using root Privileges

- Use `useradd anil` to create a user anil
- Create a sudo configuration that allows anil to perform common user management tasks
  - Allow using `useradd, usermod and userdel`
  - Allow changing passwords, but not the password for user root
- Ensure that the user only needs to enter a password for `sudo` operations every 60 minutes

### Lesson 8 Lab Solution: Using root Privileges

```bash
sudo vim /etc/sudoers.d/anil

anil    ALL=/usr/sbin/useradd, /usr/sbin/usermod, /usr/sbin/userdel, /usr/bin/passwd, ! /user/bin/passwd root

sudo visudo 

# Add the below line and then save the file and close.
Defaults timestamp_type=global,timestamp_timeout=60

su - anil
sudo useradd john
sudo passwd john
```
