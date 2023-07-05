# Lesson 24: Managing SSH

- [Lesson 24: Managing SSH](#lesson-24-managing-ssh)
    - [24.1 Understanding SSH Key-based Login](#241-understanding-ssh-key-based-login)
    - [24.2 Setting up SSH Key-based Login](#242-setting-up-ssh-key-based-login)
      - [Understandig the Procedure](#understandig-the-procedure)
    - [24.3 Caching SSH Keys](#243-caching-ssh-keys)
      - [Understandig ssh-agent](#understandig-ssh-agent)
    - [24.4 Defining SSH Client Configuration](#244-defining-ssh-client-configuration)
      - [Understandig SSH Client Options](#understandig-ssh-client-options)
    - [24.5 Exploring Common SSH Server Options](#245-exploring-common-ssh-server-options)
      - [Understandig Common SSH Server Options](#understandig-common-ssh-server-options)
    - [24.6 Copying Files Securely](#246-copying-files-securely)
    - [24.7 Synchronizing Files Securely](#247-synchronizing-files-securely)
    - [Lesson 24 Lab: Managing SSH](#lesson-24-lab-managing-ssh)
    - [Lesson 24 Lab Solution: Managing SSH](#lesson-24-lab-solution-managing-ssh)

### 24.1 Understanding SSH Key-based Login

- Explation

### 24.2 Setting up SSH Key-based Login

#### Understandig the Procedure
- **ssh-keygen** creates a public/private key pair for the current user.
  - Setting a passphrase for the private key makes it more secure, but less convenient.
- **ssh-copy-id** copies the public key over to the target server.

```bash
sudo vim /etc/hosts;

192.168.1.20    server.example.com  server

ping 192.168.1.20
ssh-keyget;
enter
enter
enter

ssh-copy-id server;
yes;
password: xxxxxx
ssh server;
exit;
```

### 24.3 Caching SSH Keys

#### Understandig ssh-agent

- When using passphrases, entering a passphrase for eery single command is inconvenient.
- **ssh-agent /bin/bash** allocates space in the bash shell to cache the private key passphrase.
- **ssh-add** adds the current passphrase to the cache.
- Cached passphrase stay available for the rest of the session duration.

```bash
sudo chvt 4;
ssh-keygen;
enter
enter
pass.word
pass.word

ssh-copy-id server;
pw: pass.word
ssh server;
pw: pass.word
exit;
ssh server ls;
pw: pass.word
ssh-agent /bin/bash;
ssh-add;
Enter passphrase: pass.word

ssh server whoami;
exit;
sudo chvt 2;
ssh server;
exit;
ssh server;
```

### 24.4 Defining SSH Client Configuration

#### Understandig SSH Client Options

- SSH client options can be set on the command line.
  - **ssh -X** enables X forwarding. Forwarded sessions are subject to X11 security extensions controls.
  - **ssh -Y** enables trusted X forwarding.
- SSH client options can be set persistently in /etc/ssh/ssh_config or ~/.ssh/ssh_config

```bash
ssh server gedit;
ssh -X server gedit;
sudo -i;
vim /etc/ssh/ssh_config;
```

### 24.5 Exploring Common SSH Server Options

#### Understandig Common SSH Server Options

- Server options are set in /etc/ssh/sshd_config.
  - Port 22
  - PermitRootLogin
  - PubkeyAuthentication
  - PasswordAuthentication
  - X11Forwarding
  - AllowUsers

```bash
vim /etc/ssh/sshd_config;
```

### 24.6 Copying Files Securely

- **scp** can be used to securely copy files over the network, using the **sshd** process
  - scp file1 file2 anil@remoteserver:/home/anil
  - scp -r root@remoteserver:/tmp/files .
- **sftp** offers an FTP client interface to securely transfer files using SSH.
  - Use `put /my/file` to upload file
  - Use `get /your/file` to download a file to the current directory.
  - Use `exit` to close an sftp session.

```bash
scp /etc/hosts anil@server:/tmp
ssh anil@server ls -l /tmp/hosts
```

### 24.7 Synchronizing Files Securely

- **rsync** is using SSH to synchronize files.
- If source and target files already exists, **rsync** will only synchronize their differences.
- The **rsync** command can be used with many options, of which the following are most common.
  - **-r** will recursively synchronize the entire directory tree.
  - **-l** synchronize symbolic links.
  - **-p** preserves symbolic links.
  - **-n** will do a dry run before actually synchronizing.
  - **-a** uses archive mode, which is equivalent to **-rlptgoD**
  - **-A** uses archive mode and also synchronizes ACLs.
  - **-X** will synchronize SELinux context as well.

```bash
ssh server ls
rsync -a * server:/home/anil/
ssh server ls
```

### Lesson 24 Lab: Managing SSH

- Set up your SSH server in such a way that.
  - SSH offers services on port 22.
  - User root is allowed to log in.
  - Grahical sessions can be forwarded.
- Verify that X forwarding works from the SSH client.
- To perform this lab, use localhost as the server address.

### Lesson 24 Lab Solution: Managing SSH

```bash
sudo -i;
vim /etc/ssh/sshd_config;
Port 22
PermitRootLogin yes
X11Forwarding yes

save and close the file.

systemctl restart sshd.service;
ssh root@localhost
ssh -X localhost gedit
```
