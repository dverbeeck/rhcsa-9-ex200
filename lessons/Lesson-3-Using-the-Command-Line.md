# Lesson 3: Using the Command Line

- [Lesson 3: Using the Command Line](#lesson-3-using-the-command-line)
    - [3.1 Working with the Bash Shell](#31-working-with-the-bash-shell)
      - [Understanding the Shell](#understanding-the-shell)
      - [Using a Shell](#using-a-shell)
    - [3.2 Exploring the GNOME Graphical Environment](#32-exploring-the-gnome-graphical-environment)
    - [3.3 Using Virtual Terminals](#33-using-virtual-terminals)
      - [Understanding Terminals](#understanding-terminals)
      - [Working with terminals](#working-with-terminals)
    - [3.4 Starting with Linux Commands](#34-starting-with-linux-commands)
      - [Using Linux Commands](#using-linux-commands)
    - [Lesson 3 Lab: Using the Command Line](#lesson-3-lab-using-the-command-line)
    - [Lesson 3 Lab Solution: Using the Command Line](#lesson-3-lab-solution-using-the-command-line)

### 3.1 Working with the Bash Shell

#### Understanding the Shell

- The shell is the environment where the user interfaces with the operating system.
- **bash** is the default command line shell.
- GNOME is the standard graphical shell.
- From a graphical shell, a terminal window running **bash** can be started.
- The **bash** shell provides many features to make working with the shell easier.
  - Command line completion
  - History
  - Variables

#### Using a Shell

- Before a user gets access to the shell, the user must authenticate.
- On a non-graphical system the user will see a text prompt, asking for the user name and password.
- In a graphical shell, the user can select the user account and password.

### 3.2 Exploring the GNOME Graphical Environment

- Log in and explain about graphical user interface.

### 3.3 Using Virtual Terminals

#### Understanding Terminals

- A terminal is an environment that runs a shell.
- In a graphical environment, multiple terminals can be used on the same screen.
- In a text-based environment, one terminal is started from the console.
- Virtual terminals can be used to start additionl terminal sessions, which are accessible by using Ctrl[-Alt]-Fn keyboard sequences.

#### Working with terminals

- Use the **who** or **w** command to see which users currently are active on which terminals.
- Use **chvt** to change between virtual terminals.

### 3.4 Starting with Linux Commands

#### Using Linux Commands

- Linux commands have common properties.
- Most commands can be used with arguments, which further specify the use of the command.
- Options can be used to modify the behavior of the command
- To get a short usage overview, use **--help** with the command.
```bash
ls --help
ls --help | less
ls -a
```

### Lesson 3 Lab: Using the Command Line

- Log in to the graphical system using your default user account.
- Open a terminal window and type the **who** command to find out which users are active.
- Use the command **who --help** to get a short usage overview for this command.  

### Lesson 3 Lab Solution: Using the Command Line

```bash
ls --help
ls -a
ls --all
who;
who --help;
w;
sudo chvt 3;
sudo chvt 4;
sudo chvt 5;
chvt 2; # Bring back to tty2.
```
