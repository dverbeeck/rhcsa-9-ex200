# Lesson 7: Managing Text Files

- [Lesson 7: Managing Text Files](#lesson-7-managing-text-files)
    - [7.1 Exploring Common Text Tools](#71-exploring-common-text-tools)
    - [7.2 Using grep](#72-using-grep)
    - [7.3 Applying Regular Expressions](#73-applying-regular-expressions)
      - [Why Regular Expressions are Confusing](#why-regular-expressions-are-confusing)
      - [Regular Expressions](#regular-expressions)
    - [7.4 Exploring awk](#74-exploring-awk)
    - [7.5 Using sed](#75-using-sed)
    - [Lesson 7 Lab: Working with Text Files](#lesson-7-lab-working-with-text-files)
    - [Lesson 7 Lab Solution: Working with Text Files](#lesson-7-lab-solution-working-with-text-files)

### 7.1 Exploring Common Text Tools

- `more` was the original file pages.
- `less` was developed to offer some more advanced features
- As a reaction to that, `more` was developed a bit more
- But still, to do more, use `less`
- In particular, in `less` you can easily scroll forwards as well as backwards in files
- Use `head` to show the first 10 lines of a text file
- Ues `tail` to show the last 10 lines
- Use -[n ] nn to sepcify another number of lines
- `cat` dumps text file contents on screen
  - `-A` shows all non-pribtable characters
  - `-b` numbers lines
  - `-s` suppresses repeated empty lines
- `tac` is doing the same, but in reversed order
- `cut` filter output: `cut -d : -f 1 /etc/passwd`
-  `sort` sort output `cut -d : -f 1 /etc/passwd | sort`
-  `tr` translates `echo hello | tr [:lower:] [:upper:]`

```bash
head /etc/passwd
head -3 /etc/passwd
tail -3 /etc/passwd
tail -3 /etc/passwd | head -n 1
cat -A /etc/hosts
cat -Ab /etc/hosts
cat -s /etc/hosts
tac /etc/hosts
cut -d : -f 1 /etc/passwd
cut -d : -f 1 /etc/passwd | sort
echo hello | tr a-z A-Z
echo hello | tr [:lower:] [:upper:]
```

### 7.2 Using grep

- `grep` is excellent to find text in files or in output

```bash
ps aux
ps aux | grep ssh
ps aux | grep ssh | grep -v grep # excluding the grep from the result
grep anil *
grep anil * 2> /dev/null
grep -i anil *
grep -A 5 -B 5 Allow /etc/ssh/sshd_config
grep -Rl root *
```

### 7.3 Applying Regular Expressions

- Regular Expressions are text patterns that are used by tools like grep and others
- Always put your regex between single quotes
- Don't confuse regular expressions with globbing (shell wildcards)
- They look like file globbing, but they are not the same
  - `grep 'a*' *`
- For use with specific tools only (`grep, vim, awk, sed`)
- See `man 7` regex for details

#### Why Regular Expressions are Confusing 

- Basic regular expressions work with most tools
- Extended regular expressions don't always work. use `grep -E` if it is an extended regular expression
- Some scripting languges (like perl) come with their own regular expressions

#### Regular Expressions

- `^` beginning of the line: `grep '^l' myfile`
- `$` end of the line: `grep 'anil$' myfile`
- `\b` end of word: `grep '^lea\b' myfile` will find lines starting with lea, but not with leanne
- `*` zero or more times: `grep 'n.*x' myfile`
- `+` one or more times (extended regex): `grep -E 'bi+t' myfile` 
- `?` zero or one time (extended regex): `grep -E 'bi?t' myfile`
- `n\{3\}` n occurs 3 times: `grep 'bon\{3\}nen' myfile`
- string must be a work: `grep '\banna\b' myfile`
- `.` one character: `grep '^.$' myfile`
- either option: `grep -E '( svm | vmx)' /proc/cpuinfo`


```bash
cat users
grep 'l' users
grep '^l' users
grep 'anna$' users
grep 'anna\b' users
grep 'b.*t' users
grep 'b.+t' users
grep -E 'b.+t' users
grep 'b.?t' users
grep 'bo\{3\}t' users
grep 'bo\{4\}t' users
grep -E '{ svm | vmx }' /proc/cpuinfo
```

### 7.4 Exploring awk

- `awk` is a pwerful text processing utility that is specialized in data extraction and rporting
- It can perform actions based on selctors

```bash
awk -F : '{ print $4 }' /etc/passwd
awk -F : '/anil/ { print $4 }' /etc/passwd
awk -F : '/student/ { print $4 }' /etc/passwd
awk -F : '{ print $NF }' /etc/passwd  # Print last column
```

### 7.5 Using sed

- `sed` is the stream editor, used to search and transform text.
- It can be used to search for text, and perform and operation on matching text.
```bash
sed -n 5p /etc/passwd
sed -i s/old/new/g ~/myfile
sed -i -e '2d' ~/myfile # Delete line number 2
```

### Lesson 7 Lab: Working with Text Files

1. Use `head` and `tail` to display the fifth line of the file /etc/passwd
2. Use `sed` to display the fifth line of the file /etc/passwd (-n 5p)
3. Use `awk` in a pipe to filter the last column out of the results of the command `ps aus '{ print $MF}'
4. Use `grep` to show the names of all files in /etc that have lines that contain the text 'root' as a word.
5. Use `grep` to show all lines from all files in /etc/ that contain exactly 3 characters
6. Use `grep` to find all files that contain the string "anil", but make sure that "anilander" is not included in the result.

### Lesson 7 Lab Solution: Working with Text Files

```bash
head -5 /etc/passwd | tail -1
sed -n 5p /etc/passwd
ps aux | awk '{ print $NF }'
cd /etc
grep 'root\b' * 2> /dev/null
grep '^...$' * /dev/null
grep '\banna\b' users
```
