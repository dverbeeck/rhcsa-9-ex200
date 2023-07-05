# Lesson 1: Understanding Red Hat Enterprise Linux

- [Lesson 1: Understanding Red Hat Enterprise Linux](#lesson-1-understanding-red-hat-enterprise-linux)
    - [1.1 Exploring Linux](#11-exploring-linux)
      - [Understanding Linux](#understanding-linux)
      - [Understanding Open Source](#understanding-open-source)
      - [Open Source and Support](#open-source-and-support)
      - [Understanding Linux Distributions](#understanding-linux-distributions)
    - [1.2 Understanding Red Hat Enterprise Linux](#12-understanding-red-hat-enterprise-linux)
      - [Red Hat Enteprise Linux](#red-hat-enteprise-linux)
      - [RHEL Versions](#rhel-versions)
    - [1.3 Getting Familiar with the Red Hat Family](#13-getting-familiar-with-the-red-hat-family)
      - [The RHEL Family](#the-rhel-family)
      - [Fedora](#fedora)
      - [CentOS Stream](#centos-stream)
      - [RHEL](#rhel)
      - [Rocky Linux and Alma Linux](#rocky-linux-and-alma-linux)
      - [RHEL Versions](#rhel-versions-1)
    - [1.4 Obtaining Red Hat Enterprise Linux](#14-obtaining-red-hat-enterprise-linux)
      - [Getting RHEL](#getting-rhel)
    - [Lesson 1 Lab: Getting Red Hat Enterprise Linux](#lesson-1-lab-getting-red-hat-enterprise-linux)
    - [Lesson 1 Lab Solution: Getting Red Hat Enterprise Linux](#lesson-1-lab-solution-getting-red-hat-enterprise-linux)

### 1.1 Exploring Linux

#### Understanding Linux

- Linux is an open source operatig system that is based on UNIX
- UNIX was the leading operating system in the 1970's, 80's and 90's
- As UNIX was mainly used as servers in companies, expensive licenses needed to be pruchased for using it.
- By being open source, Linux provided a free alternative to UNIX with the same look and feel

#### Understanding Open Source

- Open source is a software development model, where the source code used to compile software programs is not proprietary and secret, but accessible to anyone.
- Open source is very successful as a development model, as anyone can analyze the source code and contribute to it.
- Open source software leads to free software
  - That's "free as in freedom of mind, not as in free beer"
- Companies can bundle open source softwrae with support licenses and still charge for their product.
- One major requirement in open source software, is that most licenses require the source code to remain accessible to everyone at all times.

#### Open Source and Support
- For use in companies, support on software is essential
- Support is available at different levels.
  - Help if something goes wrong.
  - Support by hardware vendors, who will be able to help you according to their support contracts.
  - Support by application vendors, who support their application on top of supported operating systems.
- Linux is free, and for that reason doesn't come with support automatically.
- Enterprise Linux distributions like Red Hat Enterprise Linux differentiate themselves by providing a support offering.

#### Understanding Linux Distributions

- Linux is an operating system made from a software collection that includes the Linux kernel, and additional software components.
- The additional software components are developed as indicidual open source projects.
- You could gather all the compnents yourself and compile them into a working operating system.
- Linux distributiions are doing that for you, and provide an installer, a package manager, and other components to make it easy to get started with Linx.

### 1.2 Understanding Red Hat Enterprise Linux

#### Red Hat Enteprise Linux

- Red Hat Enteprise Linux (RHEL) is the leading Linux distribution.
- It started in 1993 as a Linux distribution, and was amongst the first distribution that offered support with their software.
- Becasue of the support offering, Red Hat Enterprise Linux currently is the most successful Linux distribution for use in companies.

#### RHEL Versions

- A new major version of RHEL is released approximately every 4 years.
- The current major version of RHEL is 9.
- During the live time of a major version, minor updates are provided - typically in six-to nine-months intervals.
- Big changes are introduced in major versions, minor versions don't see any big changes.
- While a new version of RHEL is introduced, the previous version(s) will still be supported.
- See https://access.redhat.com/support/policy/updates/errata for the current RHEL support policy.

### 1.3 Getting Familiar with the Red Hat Family

#### The RHEL Family

- Red Hat participates in many open-source projects.
- One of these is Fedora, a free community distribution that is sponsored by Red Hat.
- Mature packages from Fedora are included in CentOS Stream.
- CentOS Stream is the upstream project for RHEL, where community developers can participate and contribute new software.
- RHEL is derived from CentOS Stream and offers an enterprise Linux distribution with support options.
- Rocky Linux and Alma Linux are derived from Red Hat Enterprise Linux as free community alternatives.

#### Fedora

- Fedora is a community project sponsored by Red Hat.
- The focus in Fedora is on the latest developments, and not so much on stability.
- A new version is typically released every six months.
- Fedora is an awesome solution if you want to be ahead of the changes and run Linux on modern hardware.

#### CentOS Stream

- CentOS Stream is the upstream development platform for Red Hat Enterprise Linux.
- It includes software from the Fedora project that has reached a stable state.
- Is has an expected lifecycle of five years.
- Due to its over-evolving nature, CentOS Stream is not a recommended distributiion to use in production environments.

#### RHEL

- Red Hat Enterprise Linux is the supported Linux distribution that is offered for use by organizations.
- It as a lifecycle of 10 years.
- It offers support on the software.
- It integrates in the Red Hat product family, where additional expertise, security, certification, and monitoring can be offered.
- Unsupported free usage is offered for up to 16 instances of Red Hat Enteprise Linux.

#### Rocky Linux and Alma Linux

- Before 2019, CentOS was a free alternative to Red Hat Enterprise Linux, where all Red Hat proprietary elements were removed.
- Nowdays, CentOS Stream is the only version of CentOS and it serves as upstream to RHEL.
- Rocky Linux was created by theoriginal founders of the CentOS project.
- Alma Linux was created by CloudLinux.
- As Red Hat offers up to 16 instances of RHEL for free, the need for free distributions like Alma and Rocky is not as big as it used to be.

#### RHEL Versions

- Red Hat Enterprise Linux is the foundation for some other products as well.
- Red Hat CoreOS is an image-based container host that is used in OpenShift environments.
- Red Hat Universal Base Image (UBI) is a subset of RHEL packages that can be used as the foundation for building costom container images.

### 1.4 Obtaining Red Hat Enterprise Linux

#### Getting RHEL

- Supported and paid for instances of RHEL are offered through redhat.com or the partner ecosystem.
- Unsupported developer access to many Red Hat products is offered through developers.redhat.com
  - Developers can run up to 16 instances of RHEL for free.
  - One free evaluation copy of RHEL can be obtained through access.redhat.com

### Lesson 1 Lab: Getting Red Hat Enterprise Linux

- Create an account on developers.redhat.com and download a copy of Red Hat Enterprise Linux.

### Lesson 1 Lab Solution: Getting Red Hat Enterprise Linux

- Navigate to https://developers.redhat.com
- Choose **Log in**
- Enter email address
- Choose **Next**
- Password: Enter password
- Choose **Log in**
- Note: Sign up to a new account if you don't have one.
- Choose **Products & technologies**
- Choose **Red Hat Enterprise Linux**
- Choose **Download RHEL at no-cost**
