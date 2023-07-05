# Lesson 12: Managing Software

- [Lesson 12: Managing Software](#lesson-12-managing-software)
    - [12.1 Understanding RPM Packages](#121-understanding-rpm-packages)
      - [Managing RPM Packages](#managing-rpm-packages)
      - [Extracting RPM Packages](#extracting-rpm-packages)
    - [12.2 Setting up Repository Access](#122-setting-up-repository-access)
      - [Understanding Repositories](#understanding-repositories)
      - [Accessing Repositories](#accessing-repositories)
      - [Manually Configuring Repository Access](#manually-configuring-repository-access)
      - [Demo: Configuring the Installation Disk as Repo](#demo-configuring-the-installation-disk-as-repo)
      - [Demo: Configuring Repository Access](#demo-configuring-repository-access)
      - [Using GPG Keys](#using-gpg-keys)
    - [12.3 Managing Packages with dnf](#123-managing-packages-with-dnf)
      - [Managing Packages with dnf](#managing-packages-with-dnf)
      - [Managing Packages with dnf](#managing-packages-with-dnf-1)
    - [12.4 Using dnf Groups](#124-using-dnf-groups)
      - [Understanding Groups](#understanding-groups)
    - [12.5 Exploring Modules and Application Streams](#125-exploring-modules-and-application-streams)
      - [Understanding Modularity](#understanding-modularity)
      - [Understanding AppStream Packages](#understanding-appstream-packages)
    - [12.6 Managing dnf Updates and History](#126-managing-dnf-updates-and-history)
      - [Understanding dnf History](#understanding-dnf-history)
    - [12.7 Using subscription-manager](#127-using-subscription-manager)
      - [Understanding Subscription Manager](#understanding-subscription-manager)
      - [Understanding Entitlement Certificates](#understanding-entitlement-certificates)
    - [Lesson 12 Lab: Managing Software](#lesson-12-lab-managing-software)
    - [Lesson 12 Lab Solution: Managing Software](#lesson-12-lab-solution-managing-software)

### 12.1 Understanding RPM Packages

- Software on RHEL is installed using packages in Red Hat Package Manager (RPM) format.
- An RPM package contains a compressed archive, as well as package metadata.
- In the metadata, package dependencies are identified
- To handle dependency management, RHEL uses repositories for package installation.
- If a dependency was found, it is installed automatically fro the repository.
- Installed packages are registered in the RPM database.

#### Managing RPM Packages

- The `rpm` command can be used for some package management tasks.
- Some commands query the RPM database
  - `rpm -qa` shows all packages that are currently installed.
  - `rpm -qf filename` shows from which package filename was installed.
  - `rpm -ql` lists files installed from a package.
  - `rpm -q --scripts` shows scripts executed while installing the package.
  - `rpm -q --changelong` shows the change log for a package.
- Querying package files is also possible
  - Add `-p` to any of the commands above.

#### Extracting RPM Packages

- Contents of an RPM package can be extracted to the current directory (without installing)
  - `rpm2cpio mypackage-1.0.prm | cpio -tv` will show the contents of ap package.
  - `rpm2cpio mypackage-1.0.prm | cpio -idmv` extracts the package contents to the current directory.

```bash
rpm -qa
rpm -qa | less
which ls
rpm -qf /bin/ls
rpm -ql coreutils | grep bin
rpm -ql coreutils | grep bin | wc
rpm -q --scripts coreutils
```

### 12.2 Setting up Repository Access

#### Understanding Repositories

- A repository is a collection of RPM package files with an index that contains the repository contents.
- Repositories are often offered through web sites, but local reposiories can be creted also
- The `dnf` command is used a sthe default command to install packages from repositories.
- In RHEL 9. `dnf` is preferred over the `yum` command which was used in previous versions of RHEL.
- `dnf` and `yum` are offering the same functionality

#### Accessing Repositories

- To access repositories, a RHEL system must be registered using `subscription-manager` 
- `subscription-manager` tries to access the online Red Hat repositories
- As an alternative to online repositories, repositories can be offered thrugh Red Hat Satellite.
- If no Internet connection, nor Red Hat Satellite are available, no repositories will be available by default.
- In that case, you'll have to manually configure repository access.

#### Manually Configuring Repository Access

- To access repositories that are offered through subscription manager, use `dnf config-manager --enable name-of-the-repository` to add repository access
- Third party repositories can be added using a repo file in /etc/yum.repos.d/, or using `dnf config-manager`
  - `dnf config-manager --add-repo="file:///repo/AppStream"`
  ```bash
  cat >> /etc/yum.repos.d/AppStream.repo <<EOF
    > [AppStream]
    > name=AppStream
    > baseurl=file:///repo/AppStream
    > gpgcheck=0
    EOF
  ```

#### Demo: Configuring the Installation Disk as Repo

This procedure is NOT required on the exam. You'll need it to set up your own lab environment

  - df -h # You need 10 GB free disk space on /
  - lsblk
  - dd if=/dev/sr0 of=rhel9.iso bs=1M
  - ls -l /rhel9.iso
  - mkdir /repo
  - cp /etc/fstab /etc/fstab.bak
  - echo "/rhel9.iso  /repo iso9660 defaults  0 0" >> /etc/fstab
  - cat /etc/fstab
  - mount -a
  - ls /repo

If you don't have 10 GB free space in /
- mkdir /repo
- echo "/dev/sr0  /repo iso9660 defaults  0 0" >> /etc/fstab 

#### Demo: Configuring Repository Access

- dnf config-manager --add-repo="file:///repo/BaseOS"
- dnf config-manager --add-repo="file:///repo/AppStream"
- dnf repolist
- ls /etc/yum.repos.d/

#### Using GPG Keys

- To ensure that packages have not been tampered with, GPG keys can be used.
- A repository GPG key is used to sign all packages and before installing the package, its signature is checked
- To do this, you'll need a local GPG key to be present.
- To make accessing trusted repositories easier, use the `gpgcheck=0` option in the repository client file.

  ```bash
  cd /etc/yum.repos.d/
  ls
  vim repo_baseOS.repo
  # Update below content.
  gpgcheck=0
  ```

### 12.3 Managing Packages with dnf

#### Managing Packages with dnf

`dnf` was created to be intuitive
- `dnf list` lists installed and available packages.
  - dnf list 'selinux*'
- `dnf search` searches in name and summary. Use `dnf search all` to search in description as well.
  - `dnf search seinfo`
  - `dnf search all seinfo`
- `dnf provides` searches in package file lists for the package that provides a specific file.
  - `dnf provides */Containerfile`
- `dnf info` shows information about the package 
```bash
dnf list
dnf list | less
dnf list "selinux*"
dnf search seinfo
dnf search all seinfo
dnf provides */Containerfile
dnf info buildah-tests
```

#### Managing Packages with dnf
- `dnf install` installs packages as well as any dependencies.
- `dnf remove` removes packages, as well as packages depending on this package - potentially dangerous!
- `dnf update` compares current package version with the package version listed in the repository nd updates if necessary.
  - `dnf update kernel` will install the new kernel and keeps the old kernel as a backup.
```bash
dnf search nmap
dnf install nmap
```

### 12.4 Using dnf Groups

#### Understanding Groups

- A dnf group is a collection of packages.
- A regular group is just a collection of packages.
- An environment group is used to install a specific usage pattern, and may consist of packages and groups.
- Use `dnf group list` to see a list of groups.
- Some groups are normally only installed through environment groups and not separately, and or that reason don't show why using `dnf group list`
- Use `dnf group list hidden` to see these groups as well.
- Use `dnf group info <groupname>` to see packages within a group.
- Packages are markeed as mandatory, default, or optional
- While using `dnf group install`, only mandatory and default packages are intalled.
- To install optional packages also, use `dnf group install --with-optional`
- As group names often contin spaces, the entire group name must be referred to using double quotes.

```bash
dnf group list
dnf group info "Virtulization Host"
dnf group install --with-optional "Virtulization Host"
```

### 12.5 Exploring Modules and Application Streams

#### Understanding Modularity

- `dnf` uses modularity, meaning that different versions of the same package can be maintained in the same repository.
- Modularity is useful for packages that have a lifetime that differes from the core operating system packages.
- RHEL 9 offers 2 main repositories: 
  - BaseOS has core OS content for RHEL. Packages in BaseOS share the OS lifecycle.
  - AppStream is used for packages that don't have the same lifecycle as RHEL.

#### Understanding AppStream Packages

- AppStream packages are offered as individual packages or as modules.
- In RHEL 9.0 no modules aer provided by default, they may be offered separetely later.
- In a module, different streams can be offered, where each package version has its own stream.
- Module profiles provide common installation patterns, such as server, cient and more.

### 12.6 Managing dnf Updates and History

#### Understanding dnf History

- All transactions that `dnf` perform are logged to /var/log/dnf.rpm.log
- Use `dnf history` for a summary of all installation and removal transactions.
- Use `dnf history undo n` to undo a specific transaction

```bash
dnf history
dnf history undo 2
```

### 12.7 Using subscription-manager

#### Understanding Subscription Manager

- To use RHEL, you need to register the system and attach a subscription
  - Before completing this procedure, you'll have no repository access.
- To register, use `subscription-manager register`
  - You'll be prompted for the username of the user account to which the subscription is connected.
- To attach a subscription, use `subscription-manager attach --auto`
- To unregister a system use `subscription-manager unregister`

```bash
subscription-manager --help
subscription-manager register
subscription-manager attach
dnf repolist
```

#### Understanding Entitlement Certificates

- After attaching subscriptions to a system, entitlement Certificates are created.
  - `/etc/pki/product` indicates the installed Red Hat products.
  -  `/etc/pki/consumer` identifies the Red Hat account for registration
  - `/etc/pki/entitlement` indicates which subscription is attached.
- Use the `rct` command to check current entitlements.

```bash
rct cat-cert /etc/pki/entitlement/xxxxxx.pem
```

### Lesson 12 Lab: Managing Software

- Ensure your system is using a repository for base packges as well as application streams.
- Find the package that contains the seinfo proram file and install it.
- Download the httpd package from the repositories without installing it, and query to see if there are any scripts in it. 

### Lesson 12 Lab Solution: Managing Software

```bash
# 1
cd /etc/yum.repos.d/
rm -f *
vim base.repo

[baseos]
name=BaseOS
baseurl=file:///repo/BaseOS
pgpcheck=0

dnf repolist

vim AppStream.repo

[appstream]
name=AppStream
baseurl=file:///repo/AppStream
pgpcheck=0

dnf repolist

# 2
dnf search all seinfo
dnf install setools-console

# 3
cd /repo/AppStream
ls
cd Packages/
ls httpd*
rpm -qp --scripts httpd-2.4.xxxxx.rpm
```
