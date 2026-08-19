# Linux Process Investigation Case Study

**Lab Type:** Simulated Linux process investigation

## Scenario

A user reported that their computer was experiencing significant slowdown. During the investigation, a high-CPU process was identified and traced to a script that launched an additional executable.

## Objective

The objective of this investigation was to identify the process contributing to the performance issue, trace its parent-child process relationships, inspect the associated files, and determine the appropriate response based on the evidence collected.

## Investigation Summary

| Evidence | Finding |
|---|---|
| System performance | A Bash process was consuming significant CPU resources |
| PID 6410 | Running `/home/employee/Downloads/system_check.sh` |
| Script analysis | `system_check.sh` attempted to download and execute `/tmp/service.bin` |
| PID 6777 | `/tmp/service.bin` was confirmed running with PID 6410 as its parent |
| Authorization | `service.bin` was confirmed unauthorized |
| Response | PID 6777 was instructed to terminate gracefully and its status was verified |

## Investigation Steps

### 1. Identify Resource-Intensive Processes

I began by checking live system activity to determine which process was consuming the most CPU.

```bash
top
```

This identified a Bash process with unusually high CPU utilization. I recorded the process ID (PID) so I could investigate further.

### 2. Inspect the High-CPU Process

After identifying the PID, I used `ps` to inspect the process in more detail.

```bash
ps -fp 6410
```

The output showed that PID 6410 was running:

```text
/bin/bash /home/employee/Downloads/system_check.sh
```

The process information also provided the parent process ID (PPID), user account, and process start time.

### 3. Inspect the Associated Script

After identifying the script associated with the high-CPU process, I reviewed its file metadata.

```bash
ls -l /home/employee/Downloads/system_check.sh
```

I then inspected the contents of the script.

```bash
cat /home/employee/Downloads/system_check.sh
```

The script contained instructions to download a file named `service.bin` into `/tmp`, give the file execute permission, and attempt to run it.

### 4. Determine Whether the Downloaded File Was Running

Because the script contained instructions to execute `service.bin`, I checked the current process list for evidence that the file was actually running.

```bash
ps -ef | grep service.bin
```

The output identified `/tmp/service.bin` running as PID 6777 with PPID 6410.

I then inspected the specific process:

```bash
ps -fp 6777
```

This established a parent-child process relationship between the Bash process running `system_check.sh` and the `service.bin` process.

## Findings

The investigation established that PID 6410 was a Bash process executing `system_check.sh` and consuming significant CPU resources. Review of the script showed instructions to download `service.bin`, give it execute permission, and attempt to run it.

Process inspection later confirmed that `/tmp/service.bin` was running as PID 6777 with PID 6410 as its parent process. This provided runtime evidence connecting the Bash process executing `system_check.sh` to the `service.bin` process.

The investigation did not establish that `service.bin` was malware. However, the supervisor confirmed that the executable was unauthorized and should not have been running on the system.

## Response

After authorization from the supervisor, I attempted to terminate PID 6777 gracefully.

```bash
kill 6777
```

I then verified whether the process was still running.

```bash
ps -fp 6777
```

Verification is necessary because a process may handle or ignore the SIGTERM signal rather than terminate immediately.

## Lessons Learned

This investigation reinforced the importance of following evidence rather than making assumptions based on suspicious behavior alone. I practiced using process IDs and parent process IDs to trace relationships between running processes and used file inspection to compare intended script behavior with runtime evidence.

I also learned the importance of distinguishing between suspicious, unauthorized, and malicious activity. A process should not be classified as malware without sufficient evidence, and remediation actions should be verified after they are performed.

## Skills Demonstrated

- Linux process monitoring with `top`
- Process investigation with `ps`
- PID and PPID analysis
- File metadata inspection with `ls -l`
- Script inspection with `cat`
- Process searching with `grep`
- Graceful process termination with `kill`
- Evidence-based investigation
- Parent-child process analysis
- Verification of remediation actions
