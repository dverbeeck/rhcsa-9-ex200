# Lesson 17: Configuring Logging

- [Lesson 17: Configuring Logging](#lesson-17-configuring-logging)
    - [17.1 Exploring RHEL Logging Options](#171-exploring-rhel-logging-options)
    - [17.2 Using systemd-journald](#172-using-systemd-journald)
      - [Viewing System Journal Messages](#viewing-system-journal-messages)
      - [Viewing Boot Logs](#viewing-boot-logs)
    - [17.3 Preserving the Systemd Journal](#173-preserving-the-systemd-journal)
      - [The Need for Persistency](#the-need-for-persistency)
      - [Making the journal Persistent](#making-the-journal-persistent)
      - [Managing Persistent Journal Size](#managing-persistent-journal-size)
      - [Demo: Making the Journal Persistent](#demo-making-the-journal-persistent)
    - [17.4 Configuring rsyslogd](#174-configuring-rsyslogd)
      - [Understandig Rsyslog](#understandig-rsyslog)
      - [Understandig Facilities](#understandig-facilities)
    - [17.5 Using logrotate](#175-using-logrotate)
      - [Understandig logrotate](#understandig-logrotate)
    - [Lesson 17 Lab: Configuring Logging](#lesson-17-lab-configuring-logging)
    - [Lesson 17 Lab Solution: Configuring Logging](#lesson-17-lab-solution-configuring-logging)

### 17.1 Exploring RHEL Logging Options

- `systemd-journald` is receiving log messages from different locations.
  - Ther kernel.
  - Early boot procedure.
  - Syslog events.
  - Standard output and error from daemons.
- The systemd journal is non-persistent by default.
- The `rsyslog` service reads syslog messages and writes them to different locations.
  - Files in /var/log
  - According to output modules.
- Services may also write to /var/log

### 17.2 Using systemd-journald

#### Viewing System Journal Messages

- `systemctl status name.unit` provides easy access to the last messages that have been logged for a specific service.
- `journalctl` prints the entire jounal 
  - important messages are shown in red.
- `journalctl -p err` shows only messages with a priority error and higher.
- `journalctl -f` shows thelast 10 lies, and adds new messages while they are added.
- `journalctl -u sshd.service` shows messages for sshed.service only.
- `journalctl --since "-1 hour"; journalctl --since today` allows time specification.
- `journalctl -o verbose` adds verbose messages.

#### Viewing Boot Logs

- `journalctl -b` shows the current boot log
- `journalctl -xb` adds explanation texts to the boot log messages.
- `journalctl --list-boots` shows all boots that have been logged (on persistent journal only)
- `journalctl -b 3` shows messages from the third boot log only.

```bash
journalctl; # Press upper case G to move the cursor to the end.
journalctl -f;
journalctl -xb;
```

### 17.3 Preserving the Systemd Journal

#### The Need for Persistency

- The systemd journal is non-persistent.
- Persistency is taken care of by the rsyslog service.
- Rsyslog offers all the filtering you need to fine-tune log persistency.
- If desired, the systemd journal can be made persistent as well.

#### Making the journal Persistent

- Systemd journal settings are in /etc/systemd/journal.conf
- The setting `Storage=auto` sensures that persistent sotrage is happening automatically after creating the directory /var/log/journal
- Other options are:
  - `persistent`: stores journals in /var/log/journal
  - `volatile`: stores journals in the temporary /run/log/journal directory.
  - `none`: doesn't use any storage for the journal at all.

#### Managing Persistent Journal Size

- In journal.conf, default settngs apply to ensure that the journal can't grow in an unlimited way.
  - Log rotation triggers monthly.
  - No more than 10% of the file system size can be used.
  - No more than 15% of the file system free size can be used.
- Use `journalctl | grep -E 'Runtime Journal | System Journal'` to check current settings.

#### Demo: Making the Journal Persistent

```bash
journalctl | grep -E 'Runtime Journal | System Journal';
grep 'Storage=' /etc/systemd/journal.conf;
grep -i 'Storage=' /etc/systemd/journal.conf;
mkdir /var/log/journal;
systemctl restart systemd-journald;
ls /var/log/journal
```

### 17.4 Configuring rsyslogd

#### Understandig Rsyslog

- Rsyslog needs the `rsyslogd` service to be running.
- The main configuration file is /etc/rsyslog.conf
- Snap-in files can be placed in /etc/rsyslog.d/
- Each logger line contains three items.
  - `facility`: the specific facility that the log is created for.
  - `severity`: the severity from which should be logged.
  - `destination`: the file or other destination the log should be written to.
- Log files normally are in /var/log
- Use the `logger` command to write messages to rsyslog manually.

#### Understandig Facilities

- `rsyslog` is and must be backwards compatible with the ancient syslog service.
- In syslog, a fixed number of facilities was defined, loke kern, authpriv, corn, and more.
- To work with services that don't have their own facility local {0..7} be used.
- Because of the lack of facilities, some services take care of their own logging and don't use rsyslog.

```bash
vim /etc/rsyslog.conf;
cd /var/log;
ls -ltr .
```

### 17.5 Using logrotate

#### Understandig logrotate

- The `logrotate` command is started by a systemd timer to prevent them from growing too big.
- After roration, the file is renamed to a file with the rotation date as extension.
- When too many files are rotated, according to the settings, the oldest file will be discarded.
- Log rotation is configured in files in /etc/logrotate.conf and /etc/logrorate.d/
 
```bash
systemctl list-unit-files -t timer;
systemctl cat logrotate.service;
vim /etc/logrotate.conf;
```

### Lesson 17 Lab: Configuring Logging

- Make sure the systemd journal is logged persistently.
- Create an empty in rsyslog that writes all messages with a severity of error or higher to /var/log/error.
- Ensure that /var/log/error is rotated on a monthly basis, and the last 12 logs are kept before they are rotated out.

### Lesson 17 Lab Solution: Configuring Logging

```bash
ls /var/log/journal;
vim /etc/systemd/journal.conf;
vim /etc/rsyslog.conf;
*.err     /var/log/error
Save and close the file.

systemctl restart rsyslog.service
logger --help
logger -p err hello sweet priority;
cat /var/log/error
vim /etc/logrotate.conf
cd /et/logrotate.d/
ls
cp chrony error
vim error

/var/log/error.log {
  monthly
  create
  rotate 12
  dateext
}
```
