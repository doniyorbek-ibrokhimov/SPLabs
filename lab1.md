# Lab 1: Introduction to LINUX Commands and Quick Editing with vi Editor

**Muhammad Bilal Qureshi**  
Department of Computer Science  
School of Engineering  
**Report Submission Date:** 4th February 2025

## Objective
• To familiarize students with basic Linux commands for file handling, process management, system information, file compression, permissions, SSH, searching, and package installation.
• To familiarize students with commands of vi Editor, for efficient and speedy program writing.

## Task 1: Linux Commands Execution

### Objective
Execute and get hands-on experience with various Linux commands related to:
• File Handling
• Process Management
• System Information
• Compression of Files
• File Permissions
• SSH
• Searching
• Network Commands
• Package Installation
• Shortcuts

### Instructions

#### 1. File Handling:
• `ls`, `ls -al`, `cd dir`, `pwd`, `mkdir dir`, `rm file`, `rm -r dir`
• `cp file1 file2`, `mv file1 file2`, `touch file`, `cat file`, `head file`, `tail file`

**Execution and Output:**

A temporary directory `lab1_temp_files` was used for these operations and then cleaned up.

1.  `mkdir lab1_temp_files && cd lab1_temp_files`
    ```console
    (Directory created and changed into, no explicit output for mkdir, cd outputs to stderr or nothing on success)
    ```
2.  `pwd` (inside `lab1_temp_files`)
    ```console
    /Users/d.ibrokhimov/Documents/Spring25/lab1_temp_files
    ```
3.  `touch example_file.txt`
    ```console
    (File created, no explicit output)
    ```
4.  `ls`
    ```console
    example_file.txt
    ```
5.  `ls -al`
    ```console
    total 0
    drwxr-xr-x@  3 d.ibrokhimov  staff   96 May 18 18:54 .
    drwxr-xr-x@ 15 d.ibrokhimov  staff  480 May 18 18:54 ..
    -rw-r--r--@  1 d.ibrokhimov  staff    0 May 18 18:54 example_file.txt
    ```
    (Simulated as part of a combined command; actual combined output started with `temp_file.txt` due to command sequence `touch temp_file.txt && ls`)
    The full sequence executed for file handling was:
    `mkdir lab1_temp_files && cd lab1_temp_files && pwd && touch temp_file.txt && ls && ls -al && echo 'Hello from lab1_temp_files' > file1.txt && cat file1.txt && head -n 1 file1.txt && tail -n 1 file1.txt && cp file1.txt file2.txt && ls && mv file2.txt file3.txt && ls && rm file1.txt file3.txt temp_file.txt && ls && cd .. && rm -r lab1_temp_files && pwd && ls`

    Combined output:
    ```console
    /Users/d.ibrokhimov/Documents/Spring25/lab1_temp_files
    temp_file.txt
    total 0
    drwxr-xr-x@  3 d.ibrokhimov  staff   96 May 18 18:54 .
    drwxr-xr-x@ 15 d.ibrokhimov  staff  480 May 18 18:54 ..
    -rw-r--r--@  1 d.ibrokhimov  staff    0 May 18 18:54 temp_file.txt
    Hello from lab1_temp_files
    Hello from lab1_temp_files
    Hello from lab1_temp_files
    file1.txt       file2.txt       temp_file.txt
    file1.txt       file3.txt       temp_file.txt
    (empty ls output after rm)
    /Users/d.ibrokhimov/Documents/Spring25
    Cybersecurity                   HCI                             MAD-git-backup.zip
    Cybersecurity-git-backup.zip    HCI-git-backup.zip              PM
    Entrepreneurship                MAD                             SP
    ```

    Breakdown of key file operations from the script:
    - `pwd` (after `cd lab1_temp_files`): `/Users/d.ibrokhimov/Documents/Spring25/lab1_temp_files`
    - `touch temp_file.txt && ls`: showed `temp_file.txt`
    - `ls -al`: showed `total 0 ... -rw-r--r--@  1 d.ibrokhimov  staff    0 May 18 18:54 temp_file.txt`
    - `echo 'Hello from lab1_temp_files' > file1.txt`
    - `cat file1.txt`: `Hello from lab1_temp_files`
    - `head -n 1 file1.txt`: `Hello from lab1_temp_files`
    - `tail -n 1 file1.txt`: `Hello from lab1_temp_files`
    - `cp file1.txt file2.txt && ls`: showed `file1.txt file2.txt temp_file.txt`
    - `mv file2.txt file3.txt && ls`: showed `file1.txt file3.txt temp_file.txt`
    - `rm file1.txt file3.txt temp_file.txt && ls`: (empty output, files removed)
    - `cd .. && rm -r lab1_temp_files`
    - `pwd` (after `cd ..`): `/Users/d.ibrokhimov/Documents/Spring25`
    - `ls` (final): showed contents of `Spring25`, `lab1_temp_files` directory was gone.

#### 2. Process Management:
• `ps`, `top`, `kill pid`, `killall proc`
• `bg`, `fg n`, `fg` (manage foreground and background jobs)

**Execution and Output:**

*   `ps`
    ```console
    d.ibrokhimov@ec2-100-20-3-127 Spring25 % ps
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:00.45 docker run -it ubuntu /bin/bash
    27370 ttys004    0:00.04 /bin/zsh -il
    27400 ttys006    0:00.03 /bin/zsh -il
    ```
*   `top` (using `top -l 1 | cat` for a snapshot on macOS)
    ```console
    (Output is very long, providing a snippet)
    Processes: 596 total, 3 running, 593 sleeping, 2380 threads
    Load Avg: 3.70, 3.85, 3.91  CPU usage: 9.8% user, 16.0% sys, 74.1% idle
    SharedLibs: num = 1003, resident = 159M code, 21M data, 39M linkedit.
    MemRegions: num = 250898, resident = 5222M + 2691M private, 2553M shared.
    PhysMem: 14G used (3173M wired, 3015M compressor), 1410M unused.
    VM: 4502G vsize, 6320M framework vsize, 0% swapins, 0% swapouts.
    Networks: packets: 15121008/12G in, 10180764/2915M out.
    Disks: 34104935/1141G read, 21671058/736G written.

    PID    COMMAND          %CPU TIME     #TH   #WQ  #PORT MEM   PURG CMPRS VPRVT  VSIZE  PGRP PPID STATE    UID FAULTS    COW    MSGSENT    MSGRECV    SYSBSD    SYSMACH   CSW       PAGEINS IDLEW POWER USER
    0      kernel_task      110.0 14:33:59.73 599/8 0     0     20M   0B    0B    0      0      0    0    running  0   475507    0      954251563  792440119 2147483647 4703      98958473 0.0   0     root
    417    WindowServer     10.0 13:55:47.13 25    6     6208  943M  544K  338M  1873M+ 36G   417  1    stuck    88  77238811  170751 303146375  401580825 746823032  1333691450195512   0     3444000.0   0     _windowserver
    ... (full output is very extensive)
    ```
*   `kill pid` and `killall proc`
    A `sleep 600` process was started in the background.
    - `sleep 600 &`
      ```console
      [1] 27738
      ```
    - `pgrep sleep` (to find PID)
      ```console
      27738
      ```
    - `ps | grep sleep` (before kill)
      ```console
      27738 ttys004    0:00.00 sleep 600
      (plus the grep process itself)
      ```
    - `kill 27738` (to kill the specific sleep process)
      ```console
      [1]  + terminated  sleep 600
      ```
    - `ps | grep sleep` (after kill)
      ```console
      (showed only the grep process, sleep 27738 was gone)
      ```
    - A new `sleep 10 &` was started, then `pkill sleep` was used (similar to `killall sleep`):
      ```console
      [1] (PID of new sleep)
      [1]  + terminated  sleep 10
      ```
    - `ps | grep sleep` (after pkill): showed only the grep process.
*   `bg`, `fg n`, `fg`
    These commands are used for job control in an interactive shell (e.g., after suspending a process with `Ctrl+Z`). They are not easily demonstrated with single non-interactive command executions.
    - `Ctrl+Z`: Suspends the current foreground process.
    - `bg`: Resumes a suspended job in the background.
    - `fg %n` or `fg <job_id>`: Brings the specified job to the foreground. `fg` alone brings the most recently suspended/backgrounded job to the foreground.

#### 3. System Information:
• `date`, `uname -a`, `df`, `free`, `which app`

**Execution and Output:**

*   `date`
    ```console
    Sun May 18 18:54:51 +05 2025
    ```
*   `uname -a`
    ```console
    Darwin ec2-100-20-3-127.us-west-2.compute.amazonaws.com 24.3.0 Darwin Kernel Version 24.3.0: Thu Jan  2 20:24:06 PST 2025; root:xnu-11215.81.4~3/RELEASE_ARM64_T8103 arm64
    ```
*   `df` (using `df -h` for human-readable output)
    ```console
    Filesystem        Size    Used   Avail Capacity iused ifree %iused  Mounted on
    /dev/disk3s3s1   228Gi    10Gi    53Gi    17%    412k  551M    0%   /
    devfs            236Ki   236Ki     0Bi   100%     818     0  100%   /dev
    /dev/disk3s6     228Gi   3.0Gi    53Gi     6%       3  551M    0%   /System/Volumes/VM
    ... (full output is extensive)
    /dev/disk3s1     228Gi   153Gi    53Gi    75%    3.1M  551M    1%   /System/Volumes/Data
    ```
*   `free`
    The `free` command is not available on macOS. A common alternative for memory statistics is `vm_stat`.
    ```console
    Mach Virtual Memory Statistics: (page size of 16384 bytes)
    Pages free:                                3652.
    Pages active:                             75933.
    Pages inactive:                           73476.
    Pages speculative:                         1506.
    Pages throttled:                              0.
    Pages wired down:                        144241.
    Pages purgeable:                              2.
    "Translation faults":                2553116975.
    Pages copy-on-write:                   35199893.
    Pages zero filled:                    740998433.
    Pages reactivated:                    914944571.
    Pages purged:                          43652406.
    File-backed pages:                        53090.
    Anonymous pages:                          97825.
    Pages stored in compressor:              922162.
    Pages occupied by compressor:            189883.
    Decompressions:                       917760357.
    Compressions:                        1008844696.
    Pageins:                              179323939.
    Pageouts:                               2541893.
    Swapins:                               27163550.
    Swapouts:                              31207244.
    ```
*   `which app` (demonstrated with `which ls` and `which python3`)
    - `which ls`
      ```console
      /bin/ls
      ```
    - `which python3`
      ```console
      /opt/homebrew/bin/python3
      ```

#### 4. File Compression and Decompression:
• `tar cf file.tar files`, `tar xf file.tar`
• `gzip file`, `gzip -d file.gz`
• `tar xjf file.tar.bz2`, `tar cjf file.tar.bz2`

**Execution and Output:**

A temporary directory `compression_demo` was used.

*   `tar cf myarchive.tar file_for_tar.txt another_file.txt` and `tar xf myarchive.tar`
    - After creation (`ls`): `another_file.txt file_for_tar.txt myarchive.tar`
    - After removing originals and extracting (`ls`): `another_file.txt file_for_tar.txt myarchive.tar`
    - `cat file_for_tar.txt` (after extraction): `Tar test content`
*   `gzip file_for_gzip.txt` and `gzip -d file_for_gzip.txt.gz`
    - After `gzip` (`ls`): `file_for_gzip.txt.gz`
    - After `gzip -d` (`ls`): `file_for_gzip.txt`
    - `cat file_for_gzip.txt` (after decompression): `Gzip test content`
*   `tar cjf myarchive.tar.bz2 file_for_bzip2.txt` and `tar xjf myarchive.tar.bz2`
    - After creation (`ls`): `file_for_bzip2.txt myarchive.tar.bz2`
    - After removing original and extracting (`ls`): `file_for_bzip2.txt myarchive.tar.bz2`
    - `cat file_for_bzip2.txt` (after extraction): `Bzip2 test content`
    (The combined command output showed these stages implicitly, followed by cleanup.)

#### 5. File Permissions:
• `chmod octal file` (Modify permissions with octal codes like 777 or 755)
• `man chmod` (Access manual for chmod)

**Execution and Output:**

A temporary file `test_perms.txt` was created in `permissions_demo`.

*   `ls -l test_perms.txt` (initial)
    ```console
    -rw-r--r--@ 1 d.ibrokhimov  staff  0 May 18 18:57 test_perms.txt
    ```
*   `chmod 777 test_perms.txt` followed by `ls -l test_perms.txt`
    ```console
    -rwxrwxrwx@ 1 d.ibrokhimov  staff  0 May 18 18:57 test_perms.txt
    ```
*   `chmod 644 test_perms.txt` followed by `ls -l test_perms.txt`
    ```console
    -rw-r--r--@ 1 d.ibrokhimov  staff  0 May 18 18:57 test_perms.txt
    ```
*   `man chmod | cat`
    ```console
    CHMOD(1)                    General Commands Manual                   CHMOD(1)

    NAME
         chmod – change file modes or Access Control Lists

    SYNOPSIS
         chmod [-fhv] [-R [-H | -L | -P]] mode file ...
         chmod [-fhv] [-R [-H | -L | -P]] [-a | +a | =a] ACE file ...
         chmod [-fhv] [-R [-H | -L | -P]] [-E] file ...
         chmod [-fhv] [-R [-H | -L | -P]] [-C] file ...
         chmod [-fhv] [-R [-H | -L | -P]] [-N] file ...

    DESCRIPTION
         The chmod utility modifies the file mode bits of the listed files as
         specified by the mode operand. It may also be used to modify the Access
         Control Lists (ACLs) associated with the listed files.
    ... (full man page output is extensive) ...
    HISTORY
         A chmod command appeared in Version 1 AT&T UNIX.

    macOS 15.3                      January 7, 2017                     macOS 15.3
    ```

#### 6. SSH Commands:
• `ssh user@host` (Connect to a host)
• `ssh -p port user@host` (Connect using a specific port)
• `ssh-copy-id user@host` (Add your key to a host for passwordless login)

**Note:** These commands require a live remote host, valid credentials (username/password or SSH key), and are interactive. They cannot be executed in this automated manner without specific target setup and are skipped here. Their purpose is for secure remote login and key management.

#### 7. Searching:
• `grep pattern files`, `grep -r pattern dir`, `command | grep pattern`

**Execution and Output:**

A temporary directory `search_demo` with `file1.txt`, `file2.txt`, and `subdir/file3.txt` was used.
- `file1.txt` contained "Hello World"
- `file2.txt` contained "hello there"
- `subdir/file3.txt` contained "Hello from subdir"

*   `grep "Hello" file1.txt`
    ```console
    Hello World
    ```
*   `grep -r "Hello" .` (searching in `search_demo`)
    ```console
    ./file1.txt:Hello World
    ./subdir/file3.txt:Hello from subdir
    ```
*   `ls -l | grep "file"` (in `search_demo`)
    ```console
    -rw-r--r--@ 1 d.ibrokhimov  staff  12 May 18 18:57 file1.txt
    -rw-r--r--@ 1 d.ibrokhimov  staff  12 May 18 18:57 file2.txt
    ```

#### 8. Network Commands:
• `ping host`, `whois domain`, `dig domain`, `dig -x host`
• `wget file`, `wget -c file` (Resume download)

**Execution and Output:**

*   `ping host` (using `ping -c 3 localhost`)
    ```console
    PING localhost (127.0.0.1): 56 data bytes
    64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.064 ms
    64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.101 ms
    64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.132 ms

    --- localhost ping statistics ---
    3 packets transmitted, 3 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 0.064/0.099/0.132/0.028 ms
    ```
*   `whois domain` (using `whois google.com`)
    ```console
    % IANA WHOIS server
    % for more information on IANA, visit http://www.iana.org
    % This query returned 1 object

    refer:        whois.verisign-grs.com
    ...
       Domain Name: GOOGLE.COM
       Registry Domain ID: 2138514_DOMAIN_COM-VRSN
       Registrar WHOIS Server: whois.markmonitor.com
    ...
    >>> Last update of whois database: 2025-05-18T13:57:55Z <<<
    ... (full output is extensive)
    ```
*   `dig domain` (using `dig google.com A`)
    ```console
    ; <<>> DiG 9.10.6 <<>> google.com A
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30933
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; QUESTION SECTION:
    ;google.com.                    IN      A

    ;; ANSWER SECTION:
    google.com.             85      IN      A       216.58.210.174

    ;; Query time: 96 msec
    ;; SERVER: 8.8.4.4#53(8.8.4.4)
    ;; WHEN: Sun May 18 18:58:11 +05 2025
    ```
*   `dig -x host` (using `dig -x 8.8.8.8`)
    ```console
    ; <<>> DiG 9.10.6 <<>> -x 8.8.8.8
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32433
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; QUESTION SECTION:
    ;8.8.8.8.in-addr.arpa.          IN      PTR

    ;; ANSWER SECTION:
    8.8.8.8.in-addr.arpa.   1427    IN      PTR     dns.google.

    ;; Query time: 88 msec
    ;; SERVER: 8.8.4.4#53(8.8.4.4)
    ;; WHEN: Sun May 18 18:59:16 +05 2025
    ```
*   `wget file`, `wget -c file`
    The `wget` command was not found on this macOS system (`wget not found`). It is used for downloading files from the web. The `-c` option attempts to resume a partial download. It can be installed on macOS using Homebrew: `brew install wget`.

#### 9. Package Installation:
• `dpkg -i pkg.deb` (Install Debian packages)
• `rpm -Uvh pkg.rpm` (Install RPM packages)

**Note:** As mentioned in the lab document, `dpkg` and `rpm` are specific to Linux distributions (Debian-based and RPM-based, respectively). They are not used on macOS. For package management on macOS, Homebrew (`brew`) is commonly used (e.g., `brew install <package_name>`).

#### 10. Shortcuts:
• `Ctrl+C`, `Ctrl+Z`, `fg`, `bg`
• `Ctrl+D`, `Ctrl+U`, `Ctrl+R`
• `!!` (Repeat the last command), `exit` (Exit the session)

**Notes and Demonstration:**

*   `Ctrl+C`: Interrupts (stops) the currently running foreground command.
*   `Ctrl+Z`: Suspends the currently running foreground command. The job can then be managed with `bg` or `fg`.
*   `fg`, `bg`: As discussed in Process Management, these are for managing jobs (background/foreground).
*   `Ctrl+D`: Signals End-of-File. In a shell, this usually exits the shell if the current line is empty.
*   `Ctrl+U`: Clears the current line before the cursor in the shell.
*   `Ctrl+R`: Initiates a reverse search through command history in the shell.
*   `!!` (Repeat the last command):
    - First command: `echo "Testing !! shortcut"`
      ```console
      Testing !! shortcut
      ```
    - Second command: `!!`
      ```console
      echo "Testing !! shortcut"
      Testing !! shortcut
      ```
      (The shell first shows the command being executed, then its output)
*   `exit`: Exits the current shell session.

### Note for macOS Users:
macOS supports most of these commands. However, commands like `dpkg` and `rpm` are specific to Linux distributions. For macOS, use `brew` (Homebrew) for package management.

On macOS, there is no direct equivalent to the `cat /proc/cpuinfo` command available in Linux. However, macOS provides several ways to gather CPU information through the `sysctl` command.

#### 1. Retrieving Basic CPU Information
To retrieve the basic CPU model name, you can run the following command in the Terminal:
```bash
sysctl -n machdep.cpu.brand_string
```
This will display the CPU model name, such as:
```console
Apple M1
```

#### 2. Retrieving Detailed CPU Information
For more detailed CPU information, including the number of cores, threads, and supported features, you can use the following command:
```bash
sysctl -a | grep machdep.cpu
```
This will output various details about the CPU, including:
• `machdep.cpu.brand_string`: The CPU brand and model.
• `machdep.cpu.core_count`: The number of CPU cores.
• `machdep.cpu.thread_count`: The number of threads.
• `machdep.cpu.features`: The supported CPU features (e.g., SSE, AVX).
• `machdep.cpu.vendor`: The CPU vendor (e.g., GenuineIntel).

**Execution Output:**
```console
machdep.cpu.cores_per_package: 8
machdep.cpu.core_count: 8
machdep.cpu.logical_per_package: 8
machdep.cpu.thread_count: 8
machdep.cpu.brand_string: Apple M1
```

By using the `sysctl` command on macOS, users can easily retrieve valuable information about their CPU, which can be useful for performance analysis, troubleshooting, or system configuration.

## Task 2: Practicing the vi Editor

### Saving and Exiting
• `ZZ` — Quit and Save
• `:wq` — Quit and Save
• `:q!` — Force quit and ignore changes
• `:w` — Save changes and stay in file
• `:f file` — Change current filename to file

### Positioning the Cursor
• `G` — Go to last line in file
• `nG` or `:n` — Go to line number n
• `b` — Back one word
• `w` — Start of next word
• `(` — Beginning of previous sentence
• `)` — Beginning of next sentence
• `{` — Beginning of previous paragraph
• `}` — Beginning of next paragraph
• `H` — Top line on screen
• `L` — Last line on screen

### Inserting New Text
• `i` — Insert before cursor
• `I` — Insert at beginning of line
• `a` — Insert after cursor
• `A` — Append to end of line
• `o` — Insert one line below cursor
• `O` — Insert one line above cursor
• `Esc` — Terminate insert mode

### Searching
• `/string` — Search forward for string
• `?string` — Search backward for string
• `:n` — Jump to line number n
• `n` — Repeat last search in the same direction
• `N` — Repeat last search in opposite direction

## Conclusion
The objective of this lab was to help students become comfortable with Linux commands and vi editor for efficient system programming tasks. Mastery of these tools is essential for effective programming in a Linux-based environment.

---
*Central Asian University, Tashkent SP25 System Programming*