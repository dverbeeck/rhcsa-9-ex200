# Lesson 23: Automating with Bash Scripts

- [Lesson 23: Automating with Bash Scripts](#lesson-23-automating-with-bash-scripts)
    - [23.1 Understanding Bash Scripts](#231-understanding-bash-scripts)
      - [Understandig Shell Scripts](#understandig-shell-scripts)
      - [Bash Scripts Compared](#bash-scripts-compared)
    - [23.2 Exploring Essential Script Components](#232-exploring-essential-script-components)
      - [Using Arguments and Variables](#using-arguments-and-variables)
    - [23.3 Executing Conditional Code with if and test](#233-executing-conditional-code-with-if-and-test)
      - [Understandig test](#understandig-test)
      - [Using if, then and elso](#using-if-then-and-elso)
    - [23.4 Using for and while](#234-using-for-and-while)
      - [Understandig for and while](#understandig-for-and-while)
    - [Lesson 23 Lab: Writing Shell Scripts](#lesson-23-lab-writing-shell-scripts)
    - [Lesson 23 Lab Solution: Writing Shell Scripts](#lesson-23-lab-solution-writing-shell-scripts)

### 23.1 Understanding Bash Scripts

#### Understandig Shell Scripts

- A shell script is an executable file, used to run tasks.
- Shell scripts are used to automate common tasks.
- A shell script can be as simple a a number of commands that is sequentially executed.
- Scripts normally work with variables to make them react differently in different environemtns.
- Conditional statements such as `for`, `if`, `case` and `while` can be used to execute tasks only in specific conditions.

#### Bash Scripts Compared

- Shell scripts are common as they are easy to learn and implement.
- Also, a shell will always be available to interpret code from shell scripts.
- If the script uses internal commands only, it's fast as nothing needed to be loaded.
- There is no need to compile anything.
- For more complex work, advanced scripting languages like `python` or automation solutions like `ansible` are commonly used.


### 23.2 Exploring Essential Script Components

- To run scripts independently, store them as executable files.
- It is good practice, but not mandatory to use the **.sh** extension.
- Each script should start with **#!/bin/bash**, the so called shebang.
  - This identifies the shell that should be used to run the script code.
- Use # for comments.
- Use white lines for readability.

#### Using Arguments and Variables

- Variables are used to work with dynamic or site specific values.
- Variables can be provided in many ways.
  - By defining **key=value** inside the script.
  - By providing an argument while running the script; the value of the argument is stored in the positional parameter $1
- To define a variable no $ is needed, to reer to it put a $ in front 
  - color=red
  - echo color
  - ehco $color

```bash
vi arguments.sh

#!/bin/bash

echo You have typed $1 as the first argument.
echo There are other arguments as well, these are all arguments: $@

echo What is your name?
read NAME
echo Your name is $NAME

save and close the file.
chmod +x arguments.sh
./arguments.sh
```

### 23.3 Executing Conditional Code with if and test

#### Understandig test
- The **test** command can be used to test many things.
  - Properties of files.
  - Contents of strings.
  - Values of integers.
  - and much more.
- **test** is commonly used in **if ... then ... else** statements.
- The common use is **test condition**
- Which can also be written as [condition]

#### Using if, then and elso

- **if** statements are used to run commands only if a specific condition is ture.
- to identify the commands to be run, **then** is used.
- **else** can optionally be used to run other commands if the condition is not true.
- **fi** must be used to close an **if** statement.

```bash
man test;
vim test.sh

#!/bin/bash

if test -z "$1"
then
        echo you have to provide an argument.
        exit 3
fi

if [ "$1" = "hello" ]
then 
      echo you have typed hello.
else
      echo you have typed something else.
fi

# if test -f $1
if [ -f $1 ]
then
      echo $1 is a file.
else
      echo $1 is not a file.
fi

chmod +x test.sh
./test.sh
./test.sh hello
./test.sh /etc/hosts
```

### 23.4 Using for and while

#### Understandig for and while

- **while** is used to run commands as long as a specific condition is true.
- **for** is used to iterate over a series of elements.
  - Use to process all values of a variable.
  - Or to work on all files in a directory that match a specific pattern.

```bash
vim countdown.sh

#!/bin/bash

if test -z $1
then
      echo provide number of minutes.
      read COUNTER
else
      COUNTER=$1
fi

COUNTER=$(( COUNTER * 60 ))

while [ $COUNTER -gt 0 ]
do
    echo $COUNTER seconds remaining.
    COUNTER=$(( COUNTER - 1 ))
    sleep 1
done

echo break is over, continuing.

Save and close the file.
chmod +x countdown.sh;
./countdown.sh;
./countdown.sh 1
bash -x countdown.sh 1 # it will script debug mode.
```

### Lesson 23 Lab: Writing Shell Scripts

- Write a script that makes a copy of each file that has the *.txt extension. The copy should be named filename.txt.bak. After making the copy, the scritp should move it to the /tmp directory.
- While running the script, the directory where it should look for the files should be provided as an argument.
- If no argument was provided, the scritp should stop with exit code 9.

### Lesson 23 Lab Solution: Writing Shell Scripts

```bash
vim copy-files.sh

#!/bin/bash

if [ -z $1 ]
then 
      echo you need to specify a directory name as argument.
      exit 9
fi

for i in $1/*.txt
do
    cp $i $i.bak
    mv $i.bak /tmp
done

Save and close the file.
chmod +x copy-files.sh;
mkdir /myfiles
touch /myfiles/{anil,kumar,babu.txt}
bash -x copy-files.sh /myfiles
touch /myfiles/{1..100}.txt
bash -x copy-files.sh /myfiles
ls /tmp/*bak
```
