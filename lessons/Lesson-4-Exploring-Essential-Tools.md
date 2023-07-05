# Lesson 4: Exploring Essential Tools

- [Lesson 4: Exploring Essential Tools](#lesson-4-exploring-essential-tools)
    - [4.1 Using man](#41-using-man)
    - [4.2 Finding the Right man Page](#42-finding-the-right-man-page)
    - [4.3 Using an Editor](#43-using-an-editor)
    - [4.4 Understanding vim](#44-understanding-vim)
    - [4.5 Exploring vim](#45-exploring-vim)
    - [Lesson 4 Lab: Using Essential Tools](#lesson-4-lab-using-essential-tools)
    - [Lesson 4 Lab Solution: Using Essential Tools](#lesson-4-lab-solution-using-essential-tools)

### 4.1 Using man

```bash
man who;
man rpm; # redhat package manager.
```
- 1 Executable programs or shell commands
- 5 File formats and conventions
- 8 System administration commands

```bash
man man # Get more info about man.
man who
man 2 intor
man passwd
man -a passwd
man 5 passwd
```

### 4.2 Finding the Right man Page

```bash
man -k user

man man
/-k

man apropos
man mandb
mandb
sudo mandb # Update index for man page.
man -k user
man -k user | grep 8
```

### 4.3 Using an Editor

- nano
- vim


### 4.4 Understanding vim

- vi
- vim

### 4.5 Exploring vim

```bash
esc
i, a
o 
:wq
:q!
dd
yy
p
v
u
Ctrl-r
gg
G
/text
?text
^
$
w
:%s/old/new/g
:se number
vimtutor
```

### Lesson 4 Lab: Using Essential Tools

- Locate the **man** page that shows how to set a password.
- Use the **man** page for **useradd** and create a user with the name **anil**
- Set the password for user **anil** to **password**
- Use **vim** to create a file with the name users, and make sure that is contains the names DevOps, Linux and Windows on separate lines.

### Lesson 4 Lab Solution: Using Essential Tools

```bash
man -k password | less
man -k password | grep 1
man -k password | grep 8
man passwd
man -k authentication
man useradd
sudo useradd anna
sudo passwd anna

vim users
# Update the below user names.
alex
alexander
linda
belinga
```
- Note: Press Upper case G and Upper case G
