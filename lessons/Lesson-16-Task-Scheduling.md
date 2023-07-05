# Lesson 16: Task Scheduling

- [Lesson 16: Task Scheduling](#lesson-16-task-scheduling)
    - [16.1 Exploring RHEL Scheduling Options](#161-exploring-rhel-scheduling-options)
      - [RHEL 9 scheduling Options](#rhel-9-scheduling-options)
    - [16.2 Scheduling Tasks with Systemd Timers](#162-scheduling-tasks-with-systemd-timers)
      - [Understandig systemd Timers](#understandig-systemd-timers)
      - [Demo: Analyzing systemd Timers](#demo-analyzing-systemd-timers)
      - [Understandig Timer Activation](#understandig-timer-activation)
      - [Demo: Managing systemd Timers](#demo-managing-systemd-timers)
    - [16.3 Scheduling Tasks with cron](#163-scheduling-tasks-with-cron)
      - [Understandig cron](#understandig-cron)
      - [Using cron](#using-cron)
      - [Understandig Cron Time specification](#understandig-cron-time-specification)
    - [16.4 Understanding anacron](#164-understanding-anacron)
    - [16.5 Using at](#165-using-at)
      - [Scheduling Deferred User Jobs](#scheduling-deferred-user-jobs)
    - [16.6 Managing Temporary Files](#166-managing-temporary-files)
      - [Understandig Temporary Files](#understandig-temporary-files)
      - [Understandig systemd-tmpfiles](#understandig-systemd-tmpfiles)
      - [Managing Temporary Files](#managing-temporary-files)
      - [Demo: Managing Temporary Files](#demo-managing-temporary-files)
    - [Lesson 16 Lab: Scheduling Tasks](#lesson-16-lab-scheduling-tasks)
    - [Lesson 16 Lab Solution: Scheduling Tasks](#lesson-16-lab-solution-scheduling-tasks)

### 16.1 Exploring RHEL Scheduling Options

#### RHEL 9 scheduling Options

- `systemd` timers are the primary solution for scheduling recurring jobs on RHEL 9.
- `crond` is an older scheduling solution which is still supported.
and a bit easier to schedule custom tasks.
- `at` is available to schedule non-recurring user tasks.


### 16.2 Scheduling Tasks with Systemd Timers

#### Understandig systemd Timers

- Systemd provides unit.timer files that go together with unit.service files to schedule the service file.
- When using `systemd` timers, the timer should be enabled / started, and NOT the service unit.
- `systemd` timers are often installed from RPM packages.
- In the timer unit file, the `OnCalendar` option specifies when the service should be started.
- On RHEL 9, `systemd` timers are the default way for scheduling recurring services.

#### Demo: Analyzing systemd Timers

```bash
systemctl list-units -t timer
systemctl list-unit-files logrotate*
systemctl status logrotate.service
systemctl status logrorate.timer
dnf install -y sysstat
systemctl list-unit-files sysstat*
sar -A
systemctl cat sysstat-collect.timer
sar -A
```

#### Understandig Timer Activation

- The `systemd` timer `OnCalendar` option uses a rich language to express when the timer should activate.
  - `OnCalendar`=*:00/10 runs every 10 minutes.
  - `OnCalendar`=2023-*-* 9:9,19,29:30 runs the service every day in 2023 at 9:09:30, 9:19:30 and 9:29:30
- Use `OnUnitActivateSec` to start the unit a specific time after the unit was last activated.
- Use `OnBootSec` or `OnStartupSec` to start the unit a specific time after booting.
- Read `man 7 systemd-time` for specification of the time format to be used.

#### Demo: Managing systemd Timers

```bash
cat >> /etc/systemd/system/touchfile.service << EOF
[Unit]
Description=demo unit

[Service]
Type=oneshot
ExecStart=/usr/bin/touch /tmp/myfile.txt
EOF

cat >> /etc/systemd/system/touchfile.timer << EOF
[Unit]
Description=demo timer

[Timer]
OnCalendar=*:00/01
EOF

systemctl daemon-reload
systemctl start touchfile.timer
systemctl status touchfile.service
watch ls -l /tmp/myfile.txt
systemctl stop touchfile.timer
```

### 16.3 Scheduling Tasks with cron

#### Understandig cron

- `cron` is an old UNIX scheduling option.
- It uses `crond`, a daemon that checks its configuration to run cron jobs periodically.
- Still on RHEL 9, `crond` is enabled a a `systemd` service by default.
- Most services that need scheduling are scheduled through `systemd` timers.


#### Using cron

- The `cron` service checks its configuration every minute.
- `/etc/crontab` is the main (managed) configuration file.
- `/etc/cron.d/` is used for drop-in files.
- `/etc/cron.{hourly,daily,weekly,monthly}` is used as a drop-in for scripts that need to be scheduled on a regular basis.
  - Make sure these scripts have the execute bit set!
- User specific cron jobs can be created using `crontab -e`

#### Understandig Cron Time specification

- Cron time specification are specified as minute, hour, day of month, month, day of week.
- `0 * * dec 1-5` will run a cron job every monday through firday on minute 0 in December.
- The /etc/crontab file has a nice systax example.
- No NOT edit /etc/crontab, put drop-in files in /etc/cron.d instead.

```bash
crontab -e
10 * * * *  logger Hello
date
vim /etc/crontab
```

### 16.4 Understanding anacron

- `anacorn` is a service behind `cron` that ensures that jobs are executed on a regular basis, but not at a specific time.
- It takes care of the jobs in /etc/cron.{hourly,daily,weekly,monthly}
- Configuration is in /etc/anacrontab
- Don't change anything in /etc/anacrontab, use `systemd` timers instead.

### 16.5 Using at

#### Scheduling Deferred User Jobs

- The `atd` service must be running to run once-only jobs using `at`
- Use `at <time>` to schedule a job.
  - Type one or more job specifications in the at interactive shell.
  - Use Ctrl-D to close this shell.
- Use `atq` for a list of jobs currently scheduled.
- Use `atrm` to remove jobs from the list.

```bash
at teatime
  touch /tmp/teatime
  Ctrl-D
atq

systemctl status atd
```

### 16.6 Managing Temporary Files

#### Understandig Temporary Files

- In the past, temporary files were created in the /tmp direcotry 
- Without management, these files could stay around for a long time.
- As a solution, the /tmp directory could becreated on a RAM drive.
- Nowadays, `systemd-tmpfiles` is started while booting, and manages temporary files and directories.
- It will create and delete tmp files automatically, according to the condiguration files in the following locations:
  - /usr/lib/tmpfiles.d/
  - /etc/tmpfiles.d/
  - /run/tmpfiles.d/

#### Understandig systemd-tmpfiles

- `systemd-tmpfiles` works with related services to mange temporary files.
- `systemd-tmpfiles-setup.service` creates and removes temporary files according the configuration.
- `systemd-tmpfiles-clean.timer` calls the `systemd-tmpfiles-clean.service` to remove temporary files.
  - By default 15 minutes after booting.
  - And also on a daily basis.

#### Managing Temporary Files

- In the configuration files, specify what to do with temporary files.
  - `d /run/myfiles 0750 root root` - will create the directory /run/myfiles if necessary. No action if it already exists.
  - `D /run/myfiles 0750 root root 1d` - will crete the directory if necessary, and wipe its contents if it already exists. Files older than 1 day are eligible for autmatic removal.
- `man tmpfiles.d` provides detailed information and examples.

#### Demo: Managing Temporary Files

```bash
echo "q /tmp 1777 root root 7d" > /etc/tmpfiles.d/tmp.conf; # will create a configuration that creates the /tmp direcotry and removes all unused files after 7 days.
systemd-tmpfiles --clean /etc/tmpfiles.d/tmp.conf; # checks syntax.
echo "d /tmp/myfiles 0770 root root 30s" > /etc/tmpfiles.d/myfiles.conf;
ls -ld /tmp/myfiles;
systemd-tmpfiles --create /etc/tmpfiles.d/myfiles.conf;
ls -ld /tmp/myfiles;
touch /tmp/myfiles/test;
ls -ld /tmp/myfiles/test;
sleep 30; ls -l /tmp/myfiles/
systemd-tmpfiles --clean /etc/tmpfiles.d/tmp.conf;
ls -ld /tmp/myfiles/test; # Should be deleted.
```

### Lesson 16 Lab: Scheduling Tasks

- Ensure the systemd timer that cleans up tmp files is enabled.
- Run a corn job that will issue the command `touch /tmp/cronfile` 5 minutes from now.
- Use `at` to schedule a job to power off your system at a convenient time later today.

### Lesson 16 Lab Solution: Scheduling Tasks

```bash
systemctl list-unit-files -t timer;
systemctl cat systemd-tmpfiles-clean.timer;
crontab -e;
13  15  * * *  touch /tmp/cronfile
systemctl restart crond;
at 5 pm;
  poweroff
  Press Ctrl+D
atq;
```
