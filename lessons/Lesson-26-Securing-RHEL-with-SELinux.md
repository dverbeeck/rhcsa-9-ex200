# Lesson 26: Securing RHEL with SELinux

- [Lesson 26: Securing RHEL with SELinux](#lesson-26-securing-rhel-with-selinux)
    - [26.1 Understanding the Need for SELinux](#261-understanding-the-need-for-selinux)
    - [26.2 Using SELinux Modes](#262-using-selinux-modes)
      - [Managing SELinux States](#managing-selinux-states)
      - [Managing SELinux States while Booting](#managing-selinux-states-while-booting)
    - [26.3 Exploring SELinux Components](#263-exploring-selinux-components)
      - [Managing SELinux](#managing-selinux)
    - [26.4 Using File Context Labels](#264-using-file-context-labels)
      - [Understandig Context Labels](#understandig-context-labels)
      - [Understandig Drfault File Context](#understandig-drfault-file-context)
      - [Managing File Context Labels](#managing-file-context-labels)
    - [26.5 Finding the Right Context](#265-finding-the-right-context)
    - [26.6 Managing SELinux Port Access](#266-managing-selinux-port-access)
      - [Managing Ports](#managing-ports)
    - [26.7 Using Booleans](#267-using-booleans)
    - [26.8 Analyzing SELinux Log Messages](#268-analyzing-selinux-log-messages)
      - [Using sealert](#using-sealert)
    - [26.9 Troubleshooting SELinux](#269-troubleshooting-selinux)
    - [Lesson 26 Lab: Managing SELinux](#lesson-26-lab-managing-selinux)
    - [Lesson 26 Lab Solution: Managing SELinux](#lesson-26-lab-solution-managing-selinux)

### 26.1 Understanding the Need for SELinux

- Linux security is built on UNIX security.
- UNIX security consists of different solutions that were never developed with current IT security needs in mind.
- Most of these solutions focus on a part of the operating system.
- SELinux provides a complete and mandatory security solution .
- The principle is that if it isn't specifically allowed, it will be denied.
- As a result, "unknown" services will always need additional configuration to enable them in an environment where SELinux is enabled.
- Known services work well with the standard SELinux configuration.

### 26.2 Using SELinux Modes

#### Managing SELinux States

- **getenforce** will show the current state.
- **setenforce** toggles between Enforcing and Permissive.
- Edit /etc/sysconfig/selinux to manage the default state of SELinux.
- Never set to disabled if this is meant as a temporary measure only.
  
#### Managing SELinux States while Booting

- SELinux state can be set at boot time using a kernel parameter.
  - **enforcing=0** will start in permissive mode.
  - **enforcing=1** will start in enforcing mode.
  - **selinux=0** disabled SELinux.
  - **selinux=1** enabled SELinux.
- Access the Grub bootloader prompt to change the settings while booting.

```bash
getenforce;
setenforce permissive;
getenforce;
cat /etc/sysconfig/selinux;
reboot;

Edit the grup and set **selinux=0** parameter and boot.
getenforce;
setenforce permissive; # It will error out because selinux is disabled during the boot up.
reboot;
```

### 26.3 Exploring SELinux Components

- SELinux works with context labels.
- Context labels defind sepcific permissions.
- Context labels are applied to source objects and target objects.
- Source objects are.
  - Users.
  - Processes.
- Target objects are.
  - Files and directories.
  - Ports.
- The SELinux policy has many rules to define which source context has access to which target context.

#### Managing SELinux

- Context management is about applying contexts to mostly files, directories and ports.
- You'll need to apply a context that matches a specific rule.
- Booleans allow parts of the SELinux policy to be rewritten to allow or disallow specific functionality in an easy way.

### 26.4 Using File Context Labels

#### Understandig Context Labels

- Every objects is labeled with a context lable.
  - **user**: user specific context.
  - **role**: role specific context.
  - **type**: flags which type of operation is allowed on this object.
- In most configurations, only context type matters, so you can safely ignore user and role for RHCSA.
- Many commands support a **-Z** option to show current context information.
- Context types are used in the rules in the policy to difine which source object has access to which target object.

#### Understandig Drfault File Context

- Most service don't need additional SELinux configuration if default settings are used.
- When files are created in a directory, they typically inherit the context of the parent directory.
- When files arre copied, they typically inherit the context of the parent directory.
- When files are moved, they keep the original context.

#### Managing File Context Labels

- Use **semanage fcontext** to set the file context label 
  - This will write the context to the SELinux Policy but is it not written yet to the filesystem.
  - Use **semanage fcontext -a** to set a new context lable.
  - Use **semanage fcontext -m** to modify an existing context label.
- To enforece the policy setting on the file system, use **restorecon**
- Alternatively, use **touch /.autorelabel** to relabel all files to the context that is specified in the policy.
- See **man semanage-fcontext** for documentation.
- Do NOT use **chcon** as the changes it makes may be overwritten.
- Use **semanage fcontext -l -C** to show only settings that have changed in the current policy.

```bash
cp /ets/hosts hosts;
ls -Z /etc/hosts hosts;
mv hosts /var/www/html
ls -Z /var/www/html/
restorecon -Rv /var/www/html

vim /etc/httpd/conf/httpd.conf;

# Set 
DocumentRoot "/web"
Save and close the file.

mkdir /web/
echo "hello from web."  > /web/index.html;
systemctl restart httpd;
curl localhost;

getenforce;
setenforce permissive;
curl localhost;

# Set
vim /etc/httpd/conf/httpd.conf;
<Diretory "/web">
save and close the file.
systemctl restart httpd;
curl localhost;

setenforce enforcing;
curl localhost;

grep AVC /var/log/audit/audit.log
man semanage-fcontext;
semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
ls -Z /web
restorecon -Rv /web

getenforce
curl localhost;
```

### 26.5 Finding the Right Context

- If you apply a non-default configuration, check the default configuration context setting.
- REad man pages from **selinux-policy-doc**
- Use **sealert** (covered later)

```bash
dnf search selinux;
dnf install -y selinux-policy-doc;
man -k selinux;
man -k selinux | wc;
man -k selinux | grep httpd;
man httpd_selinux; 
```

### 26.6 Managing SELinux Port Access

#### Managing Ports

- Network ports are also provided with an SELinux context label.
- The SELinux policy is configured to allow default port access.
- For any non-default port access, use **semanage port** to apply the right label to the port.
- Use the exaples section in **man semanage-port** for examples.


```bash
vim /etc/ssh/sshd_config;
# Add
Port 2022
save and close the file.
systemctl restart sshd;
systemctl status sshd;
setenforce permissive;
systemctl restart sshd;
grep AVC /var/log/audit/audit.log | grep ssh;
semanage port -a -t ssh_port_t -p tcp 2022
setenforce enforcing
systemctl restart sshd;
ss -tunap
man semanage-port;
```

### 26.7 Using Booleans

- A boolean is an easy-to-use configuration switch to enable or disable specific parts of the SELinux policy.
- For an overview of all booleans, use **semanage boolean -l** or **getsebool -a**
- To set booleans, use **setsebool -P boolean [on|off]**
- Use **semanage boolean -l -C** to see all booleans that have a non-default setting.

```bash
getsebool -a | grep ftp;
setsebool -P ftpd_anon_write on;
semanage boolean -l -C;
```

### 26.8 Analyzing SELinux Log Messages

#### Using sealert

- SELinux uses **auditd** to write log messages to the audit log.
- Messages in the audit log may be hard to interpret.
- Ensure that **sealert** is available, it interprets messages from the audit log, applies SELinux AI, and writes meaningful messages to /var/log/messages
- Run the **sealert** command, including the UUID message to get advice on how to troubleshoot specific issues.

```bash
journalctl | grep sealert; # copy the command `sealert -l xxxx-xxxxx-xxxx`
sealert -l <uuid-goes-here> | less
touch /.autorelabel;
reboot;
```

### 26.9 Troubleshooting SELinux

- If you think that SELinux is blocking access, start by using **setenforce 0** and try again. If it works now, you have confirmed that SELinux is blocking the requested activity.
- Use **grep AVC /var/log/audit/audit.log** to see raw audit messages. Look at the source context and target context.
- Install selinux-policy-doc, using **dnf install selinux-policy-doc** for additional man pages, and try to understand what you need to do.
- Confirm by using **journalctl | grep sealert** and read the alert message that was generated.

```bash
dnf provides */sealert
```

### Lesson 26 Lab: Managing SELinux

- Configure the Apache web server to bind to port 82.
- Use **mv /etc/hosts /var/www/html/** and ensure that the file gets an SELinux context that makes it readable by the Apache web server.


### Lesson 26 Lab Solution: Managing SELinux

```bash
vim /etc/httpd/conf/httpd.conf

DocumentRoot "/var/www/html"
<Direcotry "/var/www/html">
Listen 82

Save and close the file

mv /etc/hosts /var/www/html/
systemctl restart httpd;
journalctl | grep sealert;
sealert -l uuid-goes-here; # Execute the recommended command.
cd /var/www/html
ls -Z
restorecon -Rv /var/www/html;
ls -Z
systemctl restart httpd;
```
