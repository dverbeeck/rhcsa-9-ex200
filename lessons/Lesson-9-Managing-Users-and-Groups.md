# Lesson 9: Managing Users and Groups

- [Lesson 9: Managing Users and Groups](#lesson-9-managing-users-and-groups)
    - [9.1 Understanding the Purpose of User Accounts](#91-understanding-the-purpose-of-user-accounts)
    - [9.2 Setting User Properties](#92-setting-user-properties)
    - [9.3 Creating and Managing Users](#93-creating-and-managing-users)
    - [9.4 Defining User Default Settings](#94-defining-user-default-settings)
    - [9.5 Limiting User Access](#95-limiting-user-access)
    - [9.6 Managing Group Membership](#96-managing-group-membership)
    - [9.7 Creating and Managing Groups](#97-creating-and-managing-groups)
    - [9.8 Setting Password Properties](#98-setting-password-properties)
    - [Lesson 9 Lab: Managing Users and Groups](#lesson-9-lab-managing-users-and-groups)
    - [Lesson 9 Lab Solution: Managing Users and Groups](#lesson-9-lab-solution-managing-users-and-groups)

### 9.1 Understanding the Purpose of User Accounts

- A user is a security principle, user accounts are used to provide people or preceses access to system resources
- Processes are using system accounts
- People are using regular user accounts


### 9.2 Setting User Properties

- Users properties are managed in /etc/passwd
  - Name: the name of the account
  - Password: the secret that is used for authentication, may be disabled
  - UID: a unique identifier for users
  - GID: the ID of the primiary group
  - GECOS: additional non-mandatory information about the user
  - Home Direcotry: the environment where users create personal files
  - Shell: the program that will be started after successful authentication

```bash
cat /etc/passwd
```

### 9.3 Creating and Managing Users

- useradd: create user accounts
- usermod: modify user accounts
- userdel: delete user accounts
- passwd: set passwords

```bash
sudo -i
useradd --help
useradd anil -u 2000 -s /sbin/nologin # Never allow this user to login.
grep anil /etc/passwd
userdel -rf anil
```

### 9.4 Defining User Default Settings

- use userafdd -D to sepcify default settings
- Settins in /etc/default/useradd appyl to `useradd` only
- Alternatively, write default settings to `/etc/login.defs`
- Files in `/etc/skel` are created to the user home directory opon creation

```bash
vim /etc/login.defs
change 99999 to 99 in PASS MAX DAYS line
save and close the file
touch /etc/skel/hello
useradd anil
passwd anil
ls /home/anil
cat /etc/passwd
cat /etc/shadow
```

### 9.5 Limiting User Access

- User accounts can be temporarily locked
  - `usermod -L anil` will lock user anil
  -  `usermod -U anil` will unlock user anil
- User accounts can be set to expire alos
  - `usermod -e 2032-01-01 anil` expires user account anil on 01-01-2032
- Set `/sbin/nlogin` as the shell for users that are not intended to log in at all
  - `usermod -s /sbin/nologin anil`

```bash
usermod -L anil
passwd -S anil
usermod -U anil
passwd -S anil
usermod -L anil
grep anil /etc/shadow # Put ! mark infornt of the password field to disable user login
usermod --help
```

### 9.6 Managing Group Membership

- Each user must be a a member of at least one group
- Primary Group Membership is managed through /etc/passwd
- The user primary group becomes group-owner i a user creates a file
- Additional (secondary) groups can be defined as well
- Secondary Group Membership is managed through /etc/groups
- Temporarily set primary group memebership using `newgrp`
- Use `id` to see which groups a user is a member of

### 9.7 Creating and Managing Groups

- Use `groupadd` to add groups
- `groupdel` and `groudmod` can be used to delete and modify groups
- use `lid -g groupname` to list all users that are members of a sepcific group

```bash
id anil
groupadd sales
usermod --help
usermod --help
groupmod -U anil sales
id anil
lid -g sales
lid -g wheel
```

### 9.8 Setting Password Properties

- Encrypted passwords are stored in /etc/shadown
- The encrypted string shows 3 pieces of information
  - The hashing algorithm
  - The random salt
  - The encrypted has of the uesr password
- Basic password requirements are set in /etc/login.defs
- For advanced password properties, Pluggable Authentication Modules (PAM) can be used
  - Look for the pam_faillock module
- To change password settings for current users, use `chage` or `passwd` as root 

```bash
chage anil
chage --help
chage -l anil
```

### Lesson 9 Lab: Managing Users and Groups

- Make sure that new users require a password with a maximal validity of 90 days
- Ensure that while creating users, an empty file with the name newfile is created to their home direcotry
- Create users anil, kumar, babu and babureddy
- Set the passwords for anil and kumar to 'password', disable the passwords for babu and babureddy
- Create the groups profs and students, and make users anil and kumar members of profs, and babu and babureddy members of students

### Lesson 9 Lab Solution: Managing Users and Groups

```bash
vim /etc/login.defs

change 99999 to 90 in PASS MAX DAYS line
save and close the file

touch /etc/skel/newfile

useradd anil
useradd kumar
echo password | passwd --stdin anil
echo password | passwd --stdin kumar
passwd --help
passwd -l babu
passwd -l babureddy

groupadd profs
groupadd students

groupadd --help
groupmod -U anil,kumar profs
lid -g profs
groupmod -U babu,babureddy students
lid -g students
```
