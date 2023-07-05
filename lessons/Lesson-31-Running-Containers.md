# Lesson 31: Running Containers

- [Lesson 31: Running Containers](#lesson-31-running-containers)
    - [31.1 Understanding Containers](#311-understanding-containers)
      - [Containers and Linux](#containers-and-linux)
      - [Understandig Rootless Containers](#understandig-rootless-containers)
      - [Containers and Microservices](#containers-and-microservices)
      - [Understandig Red Hat Container Tools](#understandig-red-hat-container-tools)
    - [31.2 Managing Images](#312-managing-images)
      - [Using Images and registries](#using-images-and-registries)
      - [Using REgistries](#using-registries)
      - [Accessing Red Hat Registries](#accessing-red-hat-registries)
      - [Configuring Registry Access](#configuring-registry-access)
      - [Using Contaierfile](#using-contaierfile)
      - [Demo: Building an image from a Containerfile](#demo-building-an-image-from-a-containerfile)
    - [31.3 Running Containers](#313-running-containers)
      - [Understandig Container Commands](#understandig-container-commands)
      - [Common podman Commands](#common-podman-commands)
      - [Demo: Runnign Containers](#demo-runnign-containers)
      - [troubleshoot Containers](#troubleshoot-containers)
    - [31.4 Mapping Ports and Configuring Variables](#314-mapping-ports-and-configuring-variables)
      - [Managing Environment Variables](#managing-environment-variables)
      - [Demo: Using Environemtn Variables](#demo-using-environemtn-variables)
      - [Configuring Application Access](#configuring-application-access)
      - [Demo: Mapping Port](#demo-mapping-port)
    - [31.5 Providing Persistent Storage](#315-providing-persistent-storage)
      - [Managing Storage](#managing-storage)
      - [Rootless Containers and Namespaces](#rootless-containers-and-namespaces)
      - [Understandig Non-root user Mappings](#understandig-non-root-user-mappings)
      - [Demo: Bind Mounting in Rootless Containers](#demo-bind-mounting-in-rootless-containers)
      - [Configuring SELinux for Shared Directories](#configuring-selinux-for-shared-directories)
      - [Demo: Bind Mounting in Rootless Containers](#demo-bind-mounting-in-rootless-containers-1)
    - [31.6 Starting Containers as Systemd Services](#316-starting-containers-as-systemd-services)
      - [Atuostarting Containers](#atuostarting-containers)
      - [Running Systemd Services as a User](#running-systemd-services-as-a-user)
      - [Managing Containers Using Systemd Services](#managing-containers-using-systemd-services)
      - [Understandig podman generate --new](#understandig-podman-generate---new)
      - [Creating User Unit Files](#creating-user-unit-files)
      - [Demo: Autostarting User Containers](#demo-autostarting-user-containers)
      - [Surviving all Challenges](#surviving-all-challenges)
    - [31.7 Building Images from Containerfiles](#317-building-images-from-containerfiles)
    - [Lesson 31 Lab: Managing Containers](#lesson-31-lab-managing-containers)
    - [Lesson 31 Lab Solution: Managing Containers](#lesson-31-lab-solution-managing-containers)

### 31.1 Understanding Containers

- A container is like an app on a smartphone. It's a package that contains all taht is needed to run an application.
- This makes containers the solution for the dependency challenge.
- Containers are started from container images.
- Images are provided through image registries.

#### Containers and Linux

- Containers rely on features provided by the Linux operating system.
  - Control groups set limits to the amount of resources that can be used.
  - Namespaces provide isolation to ensure the container only has access to its own data and configuration.
  - SELinux enforces security.

#### Understandig Rootless Containers

- Containers need a user ID to be started on the hose computer.
- Root containers are started by the root user.
- Rootless containers are started as a non-root user.
  - Rootless containers can generate a UID dynamically, or be perconfigured to use a specific UID.
- Rootless containers have a few limitations.
  - No unlimited access to the filesystem.
  - Can't bind to privileged network ports.

#### Containers and Microservices

- Complex applications are typically composed of multiple containers.
- Normally one container runs one application.
- This offers the benefit of better manageability.
- To manage microservices, orchestration platforms like Kubernetes or Rhed Hat OpenShift are used.

#### Understandig Red Hat Container Tools

- **podman** manages containers and container images.
- **buildah** is an advanced tool to create container images.
- **skopeo** is an advanced tool to manage, copy, delete and sign images.

### 31.2 Managing Images

#### Using Images and registries

- Container images are used to package container applications with all of their deendencies.
- Images are built according to the Open Containers Initiative (OCI) specification.
- The OCI standard guarantees compatibility so that images can be used in different environemtns, like **podman** on RHEL or **docker**.
- Container images are offered through registries.

#### Using REgistries

- Public registries such as hub.docker.com provide access to community-provided container images.
- Private registries can be created to host container images internally.
- Images optimized for use in Red hat environemtns are provided through quay.io.
- Red Hat distributes certified images that are accessible only with Red Hat credentials.
  - **registry.redhat.io** is for official Red Hat products.
  - **registry.connect.redhat.com** is for third-party products.
- Red Hat container catalog (https://catalog.redht.com) is a web interface to the Red Hat images.

#### Accessing Red Hat Registries

- Red Hat registries can be accessed with a Red Hat account.
- Developer accounts (https://developers.redhat.com) do qualify.
- Use **podman login registry.redhat.io** to login to a registry.
- Use **podman login registry.redhat.io --get-login** to get your current login credentials.

```bash
podman login registry.redhat.io;
podman login registry.redhat.io --get-login;
```

#### Configuring Registry Access

- Registry access configured in /etc/containers/registries.conf
- Default registries are in the [registries.search] section
- Registries that don't have an SSL certificate are in [registries.insecure]
- A user specific registries.conf file can be created as ~/.config/containers/registries.conf

#### Using Contaierfile

- A Containerfile (previously known as Dockerfile) is a text file with instructions to build a container image.
- Containerfile have instructions to build a custom container based on a base image such as the UBI image.
- UBI is the Universal Base Image, an image that Red Hat uses for all of its products.

#### Demo: Building an image from a Containerfile

```bash
dnf install -y container-tools;
git clone https://github.com/sandervanvugt/rhcsa
podman info;
cd rhcsa;
podman images;
podman login registry.access.redhat.com;
podman build -t mymap .
podman images;
```

### 31.3 Running Containers

- Use **podman run** to run a container image.
  - It will search for the image in the configured registries.
  - If found, it will pull the image and run the container.
- Use **podman ps** to verify that the image currently is running.
- If not seen, use **podman ps -a** to also show containers that have stopped.
- Use **podman inspect** to see what is inside an image or a container.

#### Understandig Container Commands

- When started with **podman run**, the container runs its default command.
- To run an alternative command, it can often (not always) be specified as a command line argument.
  - **podman run ubi8 sleep**
- To run an imagee from a specific registry, specify the complete image name.
- Command line options for the specific **podman** command need to be specified before the name of the image.

#### Common podman Commands

- **podman search** - searches the registries for images.
- **podman run** runs a container.
- **podman stop** stops a currently running container.
- **podman ps** shows information about containers.
- **podman build** buiulds an image froma Containerfile.
- **podman images** lists images.
- **podman inspect** shows container or image details.
- **podman pull** pulls an iamge from the registry.
- **podman exec** executes a command in a running container.
- **podman rm** removes a container. 

#### Demo: Runnign Containers

```bash
podman search ubi;
podman run --name sleepy docker.io/redhat/ubi9 sleep 3600
podman ps # run it from another terminal
podman stop sleepy;
podman images;
podman run -d --name sleepy docker.io/redhat/ubi9 sleep 3600
podman rm sleepy;
podman ps -a;
```

#### troubleshoot Containers

- Troubleshoot containers is often Troubleshooting the container primary command.
- Notice that some containers run to completion and there's nothing really to troubleshoot if you don't see them.
- Use **podman inspect container** to see which command is started.
- Use **podman run -it ...** to start the container with an interactive terminal.
- Use **podman logs** to explore logs created by the container.

```bash
podman run busybox;
podman ps;
podman ps -a;
podman run mariadb;
podman ps -a;
podman logs container-id;
```

### 31.4 Mapping Ports and Configuring Variables

#### Managing Environment Variables

- Some images require site-specific information to be passed while running.
- Use **-e KEY=VALUE** to pass these values through environment variables while running the container.

#### Demo: Using Environemtn Variables

```bash
podman run --name mydb quay.io/centos7/mariadb-103-centos7
podman ps -a;
podman logs mydb;
skopeo inspect docker://quay.io/centos7/mariadb-103-centos7
podman rm mydb;
podman run --name mydb -e MYSQL_ROOT_PASSWORD=password quay.io/centos7/mariadb-103-centos7
```

#### Configuring Application Access

- Container access happens through port mappings.
- A port on the container host is exposed and forwards traffic to hte container port.
- Port mappings can be set while starting the container, but not on a container that has already been started.
- Rootless containers can only map to a non-privileged port (higher than 1024)

#### Demo: Mapping Port

```bash
- Run as non-root user.
podman login registry.access.redhat.com
podman run -d -p 8080:80 registry.access.redhat.com/ubi9/nginx-120
podman port -a;
sudo firewall-cmd --add-port=8080/tcp --permanent;
sudo firewall-cmd --reload;
```

### 31.5 Providing Persistent Storage

#### Managing Storage

- Container storage is ephemeral by nature.
- Persistent storage can be provided by creating a directory on the container host and bind-mounting that direcotry in the container using **podman run -d ... -v /hostdir:/containerdir**
- When bind-mounting directories on the host, file ownership on these directories is important.
- If a container is started by the root user, UIDs and GID on the host match the UIDs and GIDs in the container.
- For a rotless container, you'll need to make sure that the UID of the user that runs the container application is owner of the bind-mounted directory.
- Use **podman inspect** on the image to find out which user this is.

#### Rootless Containers and Namespaces

- Rootless containers are launched in a namespaces.
- The namespace provides islation, allowing the container inside the namespace to have root access which does not exist outside the namespace.
- This means that inside the container namespace different UIDs are used than outside of the namespace.
- To ensure that access is working all right, UIDs are mapped between the namespace and the host OS.
- The **podman unshare** command can be used to run commands inside the container namespace.
- To get the UID mapping, use **podman unshare cat /proc/self/uid_map**
- You will see the container root user maps to your host ordinary UID.

#### Understandig Non-root user Mappings

- To set appropriate directory ownership on bind-mounted directories for rootless containers, additional work is required.
- First, find the UID of the user that runs the main application.
  - You may be able to find this using **podman inspect imagename**
  - Otherwise, use **podman exec containername grep username /etc/passwd**
- Use **podman unshare shown nn:nn directoryname** to set the container UID as the owner of the directory on the host.
  - Notice that **directoryname** must be in the user home directory because otherwise it wouldn't be part of the user namespace.
- Use **podman unshare cat /proc/self/uid_map** to verify mapping. 
- Verify the mapped user is owner on the host, using **ls -ld /directoryname**

#### Demo: Bind Mounting in Rootless Containers

```bash
- Run as non-root user.
podman search mariadb | grep quay
podman run -d --name mydb -e MYSQL_ROOT_PASSWORD=password quay.io/centos7/mariadb-103-centos7
podman ps;
podman exec mydb grep mysql /etc/passwd
mkdir ~/mydb # must be in current user homedir.
ls -ld mydb
podman unshare chown 27:27 mydb;
ls -ld mydb
podman unshare cat /proc/self/uid_map;
ls -ld mydb
# Note: At this point ownership is set correctly, you'll next have to take care of SELinux before using podman run -v ... to bind mount the directory.
```

#### Configuring SELinux for Shared Directories

- To bind mount a host directory in the container, the container_file_t SELinux context type must be used.
- If ownership on the host directory has been configured all right, use the **:Z** option to autmatically set this context.
  - **podman run ... -v /home/student/mydb:/var/lib/mysql:Z ...**

#### Demo: Bind Mounting in Rootless Containers

```bash
# As file ownership has been taken care of in the preceding steps, you're now ready to bind mount, taking care of SELinux as well.
podman stop mydb;
podman rm mydb;
podman run -d --name mydb -e MYSQL_ROOT_PASSWORD=password -v /home/student/mydb:/var/lib/mysql:Z quay.io/centos7/mariadb-103-centos7
podman ps -a;
ls -Z /home/mydb;
ls;
```

### 31.6 Starting Containers as Systemd Services

#### Atuostarting Containers

- To automatically start containers in a stand-alone situation, you can create systemd user unit files for rootless containers and manage them with **systemctl**
- If Kubernetes or OpenShift is used, containers will be automatically started by default.

#### Running Systemd Services as a User

- Systemd user services start when a user session is opened, and close when the user session is stopped.
- Use **loginctl enable-longer** to change that behavior and start user services for a specific user (requires root privileges)
  - loginctl enable-linger anil;
  - loginctl show-user anil;
  - loginctl disable-longer anil;

#### Managing Containers Using Systemd Services

- Create a regular user account to manage all containers.
- Use **podman** to generate a user systemd file for an existing container.
- Before, **mkdir ~/.config/systemd/user; cd ~/.config/systemd/user**
- Notice the file will be generated in the current directory.
  - **podman generate systemd --name myweb --files --new**
- To generate a service file for a root container, do it from /etc/systemd/system as the current directory.

#### Understandig podman generate --new

- The **podman generate --new** option will create a new container when the systemd unit is started, and delete that container when theunit is stopped.
- Without the **--new** option, the container is not newly created or deleted when it is stopped.

#### Creating User Unit Files

- Use **podman generate** to create user-specific unit files in ~/.config/systemd/user
- Edit the file that is generated and change the **WantedBy** line such that it reads **WantedBy=default.target**
- Manage them using systemctl --user
  - **systemctl --user daemon-reload**
  - **systemctl --user enable myapp.service**(requries linger)
  - **systemctl --user start myapp.service**
- **systemctl --user** commands only work when logging in on console or SSH and do not work in sudo and su sessions.

#### Demo: Autostarting User Containers

```bash
sudo useradd anil; 
sudo passwd anil;
sudo loginctl enable-linger anil;
ssh anil@localhost;
mkdir -p ~/.config/systemd/user;
cd ~/.config/systemd/user
podman run -d --name mynginx -p 8081:80 nginx
podman ps;
pwd;
podman generate systemd --name mynginx --files --new # This will create this file container-mynginx.service.
vim container-mynginx.service # Make sure WantedBy is set to default.target
  WantedBy=default.target
systemctl --user daemon-reload;
systemctl --user enable container-mynginx.service
systemctl --user status container-mynginx.service
reboot;
sudo -i;
ps fax | less
/anil
ps faux | less # To see the user information
/anil
```

#### Surviving all Challenges

- Log in as the user that should start the container, do NOT use **su -**
  - set a password to do so.
- As that user, **mkdir -p ~/.config/systemd/user; cd ~/.config/systemd/user**
- From that directory: **podman generate --new**
- After generating, make sure that the container-name.service file has WnatedBy=default.target (not just multi-user.target)

```bash
man podman-generate
man podman-generate-systemd
```

### 31.7 Building Images from Containerfiles

```bash
sudo dnf provides */Containerfile;
sudo dnf install -y buildah-tests;
sudo find / --name "Containerfile" -exec ls -l {} \;
less /usr/share/buildah/test/system/bud/base-with-arg/Containerfile
less rhcsa/Containerfile;
podman build -t runmap rhcsa/
podman images;
podman run localhost/runmap:latest
```
### Lesson 31 Lab: Managing Containers

- Ensure that you have full access to the Red Hat container repositories.
- Run a Mariadb container in Podman, which meets the following conditions.
  - The container is started as a rooless container by user **anil**.
  - The container must be accessible at thst port **3206**.
  - The database root password should be set to **password**.
  - The container uses the name **mydb**.
  - A bind-mounted directory is accessible: the directory **/home/anil/mariadb** on the host must be mapped to **/var/lib/mysql** in the container.
  - The container must be configured such that it automatically starts as a user systemd unit upon start of the computer.

### Lesson 31 Lab Solution: Managing Containers

```bash
sudo loginctl enable-linger anil
sudo loginctl show-user anil;
mkdir ~/mydb
podman unshare chown 27:27 mydb/
ls -ld mydb; # The uid and gid shold be some thing else.

podman run -d -p 3206:3206 --name mydb -v /home/anil/mydb:/var/lib/mysql:Z -e MYSQL_ROOT_PASSWORD=password quay.io/centos7/mariadb-103-centos7
podman ps;
ls -l mydb;

mkdir -p ~/.config/systemd/user;
cd ~/.config/systemd/user;
podman generte systemd --name mydb --files --new;
vim container-mydb.service;
systemctl --user daemon-reload;
systemctl --user enable container-mydb.service;
sudo reboot;

# Login as root user.
ps faux | less
/anil
```
