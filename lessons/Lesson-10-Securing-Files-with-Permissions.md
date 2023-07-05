# Lesson 10: Securing Files with Permissions

- [Lesson 10: Securing Files with Permissions](#lesson-10-securing-files-with-permissions)
    - [10.1 Understanding Ownership](#101-understanding-ownership)
    - [10.2 Changing File Ownership](#102-changing-file-ownership)
    - [10.3 Understanding Basic Permissions](#103-understanding-basic-permissions)
    - [10.4 Managing Basic Permissions](#104-managing-basic-permissions)
    - [10.5 Configuring Shared Group Directories](#105-configuring-shared-group-directories)
    - [10.6 Applying Default Permissions](#106-applying-default-permissions)
    - [Lesson 10 Lab: Managing Permissions](#lesson-10-lab-managing-permissions)
    - [Lesson 10 Lab Solution: Managing Permissions](#lesson-10-lab-solution-managing-permissions)

### 10.1 Understanding Ownership

- To determine which permissions a user had, Linux uses ownership.
- Every file has a user-owner, a group-ower and the others entity that is also granted permissions (ugo)
- Linux permissions are not additive, if you're the owner, permissions are applied and that's all
- Use `ls -l` to display current ownership and associated permissions.
- Best practice: Set ownership before modifying permissions.

### 10.2 Changing File Ownership
 
 - Use chown user:[group] file to set user-ownership
 - Use chgrp group file to set group-ownership.

```bash
mkdir -p /data/newfiles
cd /data
ls -l
chown anil newfiles
chown anil:sales newfiles
chgrp wheel newfiles
chown :sales newfiles
```

### 10.3 Understanding Basic Permissions


| -      | Number | Files | Directory     |
|--------|--------|-------|---------------|
| Read   | 4      | open  | list          |
| Write  | 2      | modify| create/delete |
| Execute| 1      | run   | cd            |

- When **x** is applied recursively, it would make directories as well as files executable
- In recursive command, use **X** instead.
  - Directories will be granted the execute permission.
  - Files will only get the execute permission if it is set already elsewhere on the file.

### 10.4 Managing Basic Permissions

- chmod is used to manage permissions.
- It can be used in absolute or relative mode.
- chmod 750 myfile
- chmod +x myscript

```bash
cd /data
ls -l
chmod 750 newfiles
chmod g+w newfiles
chmod o+w newfiles
chmod o-w newfiles
cd /home
cd /home/anil
ls -ld .
touch rootfile
ls -l
su - anil
ls -l
rm -f rootfile
```

### 10.5 Configuring Shared Group Directories

- If members of the same group need to share files within a directory, some special permissions are required.
- The Set Group ID (SGID) permission ensures that all files created in the shared group directory are group owned by the group owner of the directory.
- The sticky bit permission ensures that only the user who is owner of the file, or the direcotry that contains the file, is allowed to delete the file.
- chmod g+x mydir will apply SGID to the directory.
- chmod +t mydir assigns sticky bit to the directory.
- In absolute mode, a four digit number is used, of which the first digit is for the special permissions.
- chmod 3770 mydir assings SGID and sticky it, as well as rwx for user and group. 

```bash
mkdir -p /data{newfiles,profs,students}
cd /data
ls -l
id anil
id kumar
su - anil
cd /data/profs
touch anil{1..5}
ls -l
exit

su - kumar
cd /data/profs
rm anil5
exit

chmod 3770 /data/profs
ls -l
su - anil
cd /data/profs
touch anil99
ls -l
exit
su - kumar
cd /data/profs
rm -f *
```

### 10.6 Applying Default Permissions

- The umask is a shell setting that subtracts the umask from the default permissions.
  - Default is set in /etc/bashrc.
  - Set user-specific overrides in ~/.bashrc
- Default permissions for file are 666
- Default permissions for directory are 777

```bash
umask
touch hello
umask 027
umask
touch again

vi .bashrc
umask 077

. .bashrc
umask
```

### Lesson 10 Lab: Managing Permissions

- Create a shared group directory structure /data/profs and data/students that meets the following conditions.
  - Members of the groups have full read and write access to their directories, others has no permissions at all.
- Modify default permission settings such that normal users have a umask that allows the user and group to write, create and execute files and directories while denying all other access to others.

### Lesson 10 Lab Solution: Managing Permissions

```bash
vim /etc/bashrc
Change umask 002 to 007

mkdir -p /data/profs
mkdir -p /data/students
ls -l

chgrp profs profs
chgrp students students
chmod 770 profs
chmod 770 students
ls -l
```
