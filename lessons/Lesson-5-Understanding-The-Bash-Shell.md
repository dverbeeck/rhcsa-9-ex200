# Lesson 5: Understanding The Bash Shell

- [Lesson 5: Understanding The Bash Shell](#lesson-5-understanding-the-bash-shell)
    - [5.1 Using I/O Redirection and Piping](#51-using-io-redirection-and-piping)
    - [5.2 Exploring History](#52-exploring-history)
    - [5.3 Using Keyboard Shortcuts](#53-using-keyboard-shortcuts)
    - [5.4 Introducing Shell Expansion](#54-introducing-shell-expansion)
    - [5.5 Escaping Special Characters](#55-escaping-special-characters)
    - [5.6 Applying Variables](#56-applying-variables)
    - [5.7 Using alias](#57-using-alias)
    - [5.8 Tuning the Bash Environment](#58-tuning-the-bash-environment)
    - [Lesson 5 Lab: Managing Bash Configuration](#lesson-5-lab-managing-bash-configuration)
    - [Lesson 5 Lab Solution: Managing Bash Configuration](#lesson-5-lab-solution-managing-bash-configuration)

### 5.1 Using I/O Redirection and Piping

```bash
>
>>
2> /dev/null
<

ls > output
cat output
who > output
cat output
ls >> output
cat output

ls alasdfsad 
ls dsflskjdflj output 2> /dev/null
cat /dev/null
```

### 5.2 Exploring History

```bash
history
up arrow
Ctrl+r
!nn
!a

history -w
history -c
histroy -d nn
```

### 5.3 Using Keyboard Shortcuts

```bash
Ctrl+c
Ctrl+d
Ctrl+a
Ctrl+e
Ctrl+r
Ctrl+l
Ctrl+u
Alt+b
Alt+f
```

### 5.4 Introducing Shell Expansion

```bash
ls *
ls a?*
ls [a-e]*
touch file{1..9}
useradd {anna,linda,anil}
cd ~
ls -l $(which ls)
echo $PATH

ls *
ls -d *
ls -d /etc/a*
ls -d /etc/a?????*
ls -d /etc/[a-e]*
```

### 5.5 Escaping Special Characters

```bash
echo $PATH
echo \$PATH
```

### 5.6 Applying Variables

```bash
env
export key=value
echo $HIST
echo $PATH
color=blue
echo $color
bash
echo $color
exit
vi .bashrc

color=green
```

### 5.7 Using alias

```bash
alias
alias key=value
alias del='rm -rf <dir-path>'
unalias alias-name
unalias del
```

### 5.8 Tuning the Bash Environment

```bash
/etc/profile
/etc/bashrc
~/.bash_profile
~/.bashrc
```

### Lesson 5 Lab: Managing Bash Configuration

- Set a vaiable **color** to value **red** and ensure that this setting is available every time your current user account logs in.
- Also create an alias that runs the command **ls -ltr** while executing the dir command.
- Ensure that the Bash History file can grow to a maximum size of 2500 entries.

### Lesson 5 Lab Solution: Managing Bash Configuration

```bash
vim .bash_profile

export color=red
alias dir='ls -ltr'
export HIST_FILE_SIZE=2500

source ~/.bash_profile

dir
echo $HIST_FILE_SIZE
echo $color
```
