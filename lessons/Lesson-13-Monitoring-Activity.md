# Lesson 13: Monitoring Activity

- [Lesson 13: Monitoring Activity](#lesson-13-monitoring-activity)
    - [13.1 Exploring Jobs and Processes](#131-exploring-jobs-and-processes)
      - [Understanding Jobs and Processes](#understanding-jobs-and-processes)
    - [13.2 Managing Shell Jobs](#132-managing-shell-jobs)
    - [13.3 Understanding Process States](#133-understanding-process-states)
      - [Understanding Runnable Process States](#understanding-runnable-process-states)
      - [Understanding Waiting Process States](#understanding-waiting-process-states)
      - [Understanding Exit States](#understanding-exit-states)
      - [Understanding Zombies](#understanding-zombies)
    - [13.4 Observing Process Information with ps](#134-observing-process-information-with-ps)
      - [Using ps](#using-ps)
    - [13.5 Monitoring Memory Usage](#135-monitoring-memory-usage)
      - [Understanding Memory Use](#understanding-memory-use)
      - [Understanding Write Cache](#understanding-write-cache)
    - [13.6 Observing CPU Load](#136-observing-cpu-load)
    - [13.7 Monitoring System Activity with top](#137-monitoring-system-activity-with-top)
      - [Using top](#using-top)
    - [Lesson 13 Lab: Observing Processes](#lesson-13-lab-observing-processes)
    - [Lesson 13 Lab Solution: Observing Processes](#lesson-13-lab-solution-observing-processes)

### 13.1 Exploring Jobs and Processes

#### Understanding Jobs and Processes

- All tasks are started a processes
- Processes have a PID
- Common Process management tasks include scheduling priority and sending signals.
- Some processes are starting multiple threads, incivicual threads connot be managed.
- Tasks that are started from a shell can be managed as jobs.
- Shell jobs can be started in the foreground or background.

### 13.2 Managing Shell Jobs

- Use `command &` to to start a job in the background.
- To move a job to the background.
  - First, stop it using `Ctrl+z`
  - Next, type `bg` to move it to the background.
- Use `jobs` for a complete overview of running jobs.
- Use `fg [n]` to move the last job back to the foreground.

```
sleep 1000
Ctrl+Z
bg
jobs
fg 1
Ctrl+C # stop
```

### 13.3 Understanding Process States

#### Understanding Runnable Process States

- When a new process is started (forked) it is scheduled and after being scheduled, it will get a runnable state (R).
  - In this state it is waiting in the queue to bescheduled.
- Runnable processes will get a time slice, which allows them to get a running Staate, in either kernel space or user space.
- Runnable processes can get preempted or rescheduled.
  - In that case, they will return to a runnable state and wait in the queqe for a new time slice.
- A runnable precess can be stopped (ctrl-z) and will show as TASK_STOPPED(T), and after being stopped it can receive another signal to resume and return to a runnable state.

#### Understanding Waiting Process States

- While running the process my have to wait.
  - This is alos referred to as "blocking" state, but "blocking"
 is not an offical state in the Linux kernel.
- Waiting processes can have different flags.
  - TASK_INTERRUPIBLE (S): the process is waiting for hardware request, system resource access or a signal.
  - TASK_UNINTERRUPIBLE (D): the process is waiting but does not respond to signals.
  - TASK_KILABLE (K): the process is waiting but may be killed.
  - TASK_REPORT_IDLE (I): Used for kernel threads, this process will not count for the load average.

#### Understanding Exit States

  - When a process exits, it will briefly enter the EXIT_ZOMBIE(Z) state. This is where it signals the parent process that it exits and all resources except for the PID are released.
  - In the next state the process will enter the EXIT_DEAD(X) state. In this state it will be reaped and all remaining processes are cleaned up.

#### Understanding Zombies

- A process becomes a Zombie when it has completed its task, but the parent process hasn't collected its execution status.
- Zombies are already dead so they can't and don't have to be killed.
- The most important disadvantages, is that Zombies occupy a PID.
- To get rid of the Zombie, the parent process must collect the child execution status.
  - Send SIGCHLD to the parent to ask the parent to reap the Zombie.
  - Kill the parent process.
  - When the parent is killed, the Zombie becomes an orphan and will be adopted by the init process.


### 13.4 Observing Process Information with ps

#### Using ps

- The `ps` command has two different dialects: BSD and System V
  - In BSD, options do not have a leading - 
  - In System V, options do have a leading -
- Therefore `ps -L` and `ps L` are two completely different commands.
- `ps` shows an overview of current processes.
- Use `ps aux` for an overview of all processes.

```
ps aux | less
```

- `ps -fax` shows hierarchical relations betwen processes.
- `ps -fU linda` shows all processes owned by linda.
- `ps -f --forest -C sshd` shows a process tree for a specific process
- `ps L` shows format specifiers
- `ps -eo pid,ppid,user,cmd` uses some of these specifiers to show a list of processes.
```bash
ps -fax
ps -fU student
ps -f --forest -C sshd | less
ps -L
ps -eo pid,ppid,user,cmd
```

### 13.5 Monitoring Memory Usage

#### Understanding Memory Use

- Linux places as many files as possible in cache to guarantee fast access to the files.
- For that reason, Linux memory often shows as saturated.
- Swap is used as a overflow buffer of emulated RAM on disk.
- The Linux kernel moves inactive application memory to swap first.
- Inactive cache memory will just be dropped.
- Use `free -m` to get details about current memory usage.
- More detailed memory information is in /proc/meminfo
```bash
free -m
reboot
Press e on the Linux boot menu.
Update this at the end of the line at ending as swap.
MEM=1G
Ctrl+X to start.
free -m
less /proc/meminfo
```

#### Understanding Write Cache

- While writing files, a write cache (buffers) is used.
- This write cache is periodically committed to disk by the `pdflush` kernel thread.
- As a result, after commiting a file write, it's not immediately secure.
- to ensure that a file is committed to disk immediately, use `sync` command.

### 13.6 Observing CPU Load

- CPU load is checked through `uptime`
- CPU load is expressed as the average number of runnable processes over the last 1, 5 and 15 minutes.
- As a rough guideline, this number should not exceed the number of CPU cores on a system.
- Use `lscpu` to check the number of CPU cores.
```bash
lscpu
uptime
dd if=/dev/zero of=/dev/null & # Run this command 10 times.
uptime
uptime
```

### 13.7 Monitoring System Activity with top

#### Using top

- `top` is a dashboard that allows you to monitor current system activity.
- Press `f` to show and select from available display fields
- type `M` to filter on memory usage
- Press `W` to save new display settings.
- `htop` is a common alternative for `top`, but not installed by default.

```bash
top
ps aux | grep defunct
top
```

### Lesson 13 Lab: Observing Processes

- Use the appropriate utilities to find out if your machine performance is in good shape.

### Lesson 13 Lab Solution: Observing Processes

```bash
top
1
```
