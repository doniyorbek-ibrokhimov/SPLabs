# Lab 9 - Process Creation, Execution, and Control in Linux using fork(), exec(), wait(), ps, nice and renice

**Muhammad Bilal Qureshi**  
**Department of Computer Science**  
**School of Engineering**  
**Report Submission Date:** In next lab

## Objective

• To understand the concepts of process creation and execution using `fork()` and `exec()` system calls.
• To differentiate between parent and child processes and observe their Process IDs (PIDs).
• To use the `wait()` system call to synchronize parent and child processes.
• To observe and manage process priorities using `ps`, `nice`, and `renice` commands.

## Tasks

### Task 1: fork() with wait() Synchronization

Write a program that creates a child using `fork()`. The child process should print decimal numbers from 0 to 9, and the parent should print alphabets from a to z. Ensure output is not jumbled by using `wait()` in the parent.

#### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    int status;
    
    pid = fork();
    
    if (pid < 0) {
        perror("Fork failed");
        exit(1);
    } else if (pid == 0) {
        printf("Child process (PID: %d) printing numbers 0-9:\n", getpid());
        for (int i = 0; i <= 9; i++) {
            printf("%d ", i);
            fflush(stdout);
            usleep(100000);
        }
        printf("\nChild process complete.\n");
        exit(0);
    } else {
        printf("Parent process (PID: %d) waiting for child (PID: %d) to complete...\n", getpid(), pid);
        wait(&status);
        printf("Child has completed. Parent process printing alphabets a-z:\n");
        for (char c = 'a'; c <= 'z'; c++) {
            printf("%c ", c);
            fflush(stdout);
            usleep(100000);
        }
        printf("\nParent process complete.\n");
    }
    
    return 0;
}
```

#### Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd /Users/d.ibrokhimov/Documents/Spring25/SP/labs && gcc -o task1 task1.c && ./task1
Parent process (PID: 38012) waiting for child (PID: 38015) to complete...
Child process (PID: 38015) printing numbers 0-9:
0 1 2 3 4 5 6 7 8 9 
Child process complete.
Child has completed. Parent process printing alphabets a-z:
a b c d e f g h i j k l m n o p q r s t u v w x y z 
Parent process complete.
```

### Task 2: Using execv to List Directories

Write a program using `execv` to list the contents of the following directories:
• `/bin`
• `/home`

Observe how `execv` replaces the current process image.

#### Implementation for `/bin` Directory

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    char *args[] = {"ls", "-la", "/bin", NULL};
    printf("Process ID before execv: %d\n", getpid());
    printf("Listing contents of /bin directory:\n");
    
    execv("/bin/ls", args);
    
    printf("This line will not be printed if execv succeeds\n");
    
    return 0;
}
```

#### Output for `/bin` Directory

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % gcc -o task2 task2.c && ./task2
Process ID before execv: 38036
Listing contents of /bin directory:
total 9440
drwxr-xr-x@ 39 root  wheel     1248 Feb  4 21:57 .
drwxr-xr-x  22 root  wheel      704 Feb  4 21:57 ..
-rwxr-xr-x   2 root  wheel   101456 Feb  4 21:57 [
-r-xr-xr-x   1 root  wheel  1293888 Feb  4 21:57 bash
-rwxr-xr-x   1 root  wheel   119040 Feb  4 21:57 cat
-rwxr-xr-x   1 root  wheel   120784 Feb  4 21:57 chmod
-rwxr-xr-x   1 root  wheel   136928 Feb  4 21:57 cp
-rwxr-xr-x   2 root  wheel  1108448 Feb  4 21:57 csh
-rwxr-xr-x   1 root  wheel   274288 Feb  4 21:57 dash
-rwxr-xr-x   1 root  wheel   135296 Feb  4 21:57 date
-rwxr-xr-x   1 root  wheel   152576 Feb  4 21:57 dd
-rwxr-xr-x   1 root  wheel   119152 Feb  4 21:57 df
-rwxr-xr-x   1 root  wheel   101184 Feb  4 21:57 echo
-rwxr-xr-x   1 root  wheel   202576 Feb  4 21:57 ed
-rwxr-xr-x   1 root  wheel   102048 Feb  4 21:57 expr
-rwxr-xr-x   1 root  wheel   101184 Feb  4 21:57 hostname
-rwxr-xr-x   1 root  wheel   101584 Feb  4 21:57 kill
-r-xr-xr-x   1 root  wheel  2566144 Feb  4 21:57 ksh
-rwxr-xr-x   1 root  wheel   362736 Feb  4 21:57 launchctl
-rwxr-xr-x   2 root  wheel   102080 Feb  4 21:57 link
-rwxr-xr-x   2 root  wheel   102080 Feb  4 21:57 ln
-rwxr-xr-x   1 root  wheel   154624 Feb  4 21:57 ls
-rwxr-xr-x   1 root  wheel   101472 Feb  4 21:57 mkdir
-rwxr-xr-x   1 root  wheel   119344 Feb  4 21:57 mv
-rwxr-xr-x   1 root  wheel   304688 Feb  4 21:57 pax
-rwsr-xr-x   1 root  wheel   170864 Feb  4 21:57 ps
-rwxr-xr-x   1 root  wheel   101296 Feb  4 21:57 pwd
-rwxr-xr-x   1 root  wheel   101072 Feb  4 21:57 realpath
-rwxr-xr-x   2 root  wheel   119216 Feb  4 21:57 rm
-rwxr-xr-x   1 root  wheel   101120 Feb  4 21:57 rmdir
-rwxr-xr-x   1 root  wheel   101232 Feb  4 21:57 sh
-rwxr-xr-x   1 root  wheel   101184 Feb  4 21:57 sleep
-rwxr-xr-x   1 root  wheel   135504 Feb  4 21:57 stty
-rwxr-xr-x   1 root  wheel   100784 Feb  4 21:57 sync
-rwxr-xr-x   2 root  wheel  1108448 Feb  4 21:57 tcsh
-rwxr-xr-x   2 root  wheel   101456 Feb  4 21:57 test
-rwxr-xr-x   2 root  wheel   119216 Feb  4 21:57 unlink
-rwxr-xr-x   1 root  wheel   101056 Feb  4 21:57 wait4path
-rwxr-xr-x   1 root  wheel  1344816 Feb  4 21:57 zsh
```

#### Implementation for `/home` Directory

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    char *args[] = {"ls", "-la", "/home", NULL};
    printf("Process ID before execv: %d\n", getpid());
    printf("Listing contents of /home directory:\n");
    
    execv("/bin/ls", args);
    
    printf("This line will not be printed if execv succeeds\n");
    
    return 0;
}
```

#### Output for `/home` Directory

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % gcc -o task2_home task2_home.c && ./task2_home
Process ID before execv: 38058
Listing contents of /home directory:
lrwxr-xr-x  1 root  wheel  25 Apr 29 14:41 /home -> /System/Volumes/Data/home
```

**Observation:** As we can see, the `execv` system call replaces the current process image with the specified command. The code after the `execv` call doesn't execute because the process is completely replaced by the new one (in this case, the `ls` command). The process ID remains the same before and after the `execv` call, which demonstrates that `execv` doesn't create a new process but replaces the existing one.

### Task 3: Same PID using exec

Write a program to demonstrate that the process ID of a parent process and the process created using `exec` remains the same (`exec` does not create a new process).

#### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

int main() {
    pid_t pid = getpid();
    char pid_str[20];
    
    printf("Process ID before exec: %d\n", pid);
    
    FILE *file = fopen("pid_before_exec.txt", "w");
    if (file) {
        fprintf(file, "%d", pid);
        fclose(file);
    }
    
    sprintf(pid_str, "%d", pid);
    
    printf("Now executing /bin/ps to show process information...\n");

    execl("/bin/ps", "ps", "-p", pid_str, NULL);
    
    printf("This line will never be printed if execl succeeds\n");
    
    return 0;
}
```

#### Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % gcc -o task3 task3.c && ./task3
Process ID before exec: 38619
Now executing /bin/ps to show process information...
  PID TTY           TIME CMD
38619 ttys019    0:00.01 ps -p 38619
```

**Verification:** When we check the content of the `pid_before_exec.txt` file, we can see that the PID before `exec` was 38619:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % cat pid_before_exec.txt
38619
```

The ps command shows the same PID (38619), demonstrating that the `exec` call does not create a new process but replaces the existing one.

### Task 4: fork() followed by execl

Write a program where a process creates a child using `fork()`. The child then uses `execl` to execute another program. Prove that the PID remains the same before and after `execl`.

#### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
#include <string.h>

int main() {
    pid_t pid;
    int status;
    
    pid = fork();
    
    if (pid < 0) {
        perror("Fork failed");
        exit(1);
    } else if (pid == 0) {
        pid_t child_pid = getpid();
        char pid_str[20];
        
        sprintf(pid_str, "%d", child_pid);
        
        printf("Child process - PID before execl: %d\n", child_pid);
        
        printf("Child process executing ps command to show PID information...\n");
        execl("/bin/ps", "ps", "-p", pid_str, NULL);
        
        printf("This line will never be printed if execl succeeds\n");
        exit(1);
    } else {
        printf("Parent process - PID: %d, Child PID: %d\n", getpid(), pid);
        printf("Parent waiting for child to complete...\n");
        
        wait(&status);
        
        printf("Child process has completed with status: %d\n", WEXITSTATUS(status));
        printf("Parent process terminating\n");
    }
    
    return 0;
}
```

#### Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % gcc -o task4 task4.c && ./task4
Parent process - PID: 38647, Child PID: 38648
Parent waiting for child to complete...
Child process - PID before execl: 38648
Child process executing ps command to show PID information...
  PID TTY           TIME CMD
38648 ttys019    0:00.00 ps -p 38648
Child process has completed with status: 0
Parent process terminating
```

**Observation:** In this program, we create a child process using `fork()` which gets a new PID (38648). The child process then prints its PID before executing `execl()`. After the `execl()` call, the `ps` command runs and shows the same PID (38648). This demonstrates that the child's PID remains the same before and after the `execl()` call, proving that `execl()` replaces the process image but keeps the same process ID.

### Task 5: Child Kills Parent

Write a program where the child created by `fork()` kills its parent using the `kill()` system call. Verify that the parent process is terminated using `ps` command.

#### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <signal.h>

int main() {
    pid_t pid;
    pid_t parent_pid = getpid();
    
    printf("Parent process started with PID: %d\n", parent_pid);
    
    pid = fork();
    
    if (pid < 0) {
        perror("Fork failed");
        exit(1);
    } else if (pid == 0) {
        pid_t child_pid = getpid();
        printf("Child process started with PID: %d\n", child_pid);
        printf("Child is going to kill parent (PID: %d) in 2 seconds...\n", parent_pid);
        
        sleep(2);
        
        printf("Child is now killing parent process...\n");
        int kill_result = kill(parent_pid, SIGTERM);
        
        if (kill_result == 0) {
            printf("Kill signal sent successfully to parent\n");
        } else {
            perror("Failed to send kill signal");
            exit(1);
        }
        
        printf("Child process is sleeping for 3 seconds before terminating\n");
        sleep(3);
        printf("Child process terminating\n");
        exit(0);
    } else {
        printf("Parent process waiting... Run 'ps -p %d' in another terminal to verify it's running\n", parent_pid);
        
        while(1) {
            sleep(1);
        }
        
        printf("This line will never be executed in parent process\n");
    }
    
    return 0;
}
```

#### Output

To verify that the child process successfully kills the parent process, we executed the program and used the `ps` command to check the parent process status before and after the kill:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % gcc -o task5 task5.c && (./task5 & PARENT_PID=$!; echo "Parent PID: $PARENT_PID"; sleep 1; echo -e "\nBefore kill - Process status:"; ps -p $PARENT_PID; sleep 3; echo -e "\nAfter kill - Process status:"; ps -p $PARENT_PID || echo "Process $PARENT_PID no longer exists (killed by child process)")
Parent PID: 39137
Parent process started with PID: 39137
Parent process waiting... Run 'ps -p 39137' in another terminal to verify it's running
Child process started with PID: 39139
Child is going to kill parent (PID: 39137) in 2 seconds...

Before kill - Process status:
  PID TTY           TIME CMD
39137 ttys019    0:00.00 ./task5
Child is now killing parent process...
Kill signal sent successfully to parent
Child process is sleeping for 3 seconds before terminating

After kill - Process status:
  PID TTY           TIME CMD
Process 39137 no longer exists (killed by child process)
```

**Observation:** The output clearly demonstrates that the child process successfully killed its parent. As shown in the terminal output, the parent process with PID 39137 was running before the kill signal was sent. After the child process sent the SIGTERM signal to the parent process, the subsequent `ps` command shows that the parent process no longer exists, confirming that it was successfully terminated by the child process using the `kill()` system call.

### Task 6: Creating a Zombie Process

Write a program where a child process terminates before its parent process. Use `sleep()` in the parent to delay its termination. Run the program in the background using `&`, then use:
• `ps -aux | grep ./your_executable`

Observe the zombie state (Z) for the child process between the child's termination and before the parent finishes.

#### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    
    pid = fork();
    
    if (pid < 0) {
        perror("Fork failed");
        exit(1);
    } else if (pid == 0) {
        printf("Child process (PID: %d) started and will terminate shortly\n", getpid());
        printf("Parent process (PID: %d) will continue running\n", getppid());
        
        exit(0);
    } else {
        printf("Parent process (PID: %d) created child (PID: %d)\n", getpid(), pid);
        printf("Parent sleeping for 10 seconds while child becomes zombie...\n");
        
        sleep(10);
        
        printf("Parent process now waiting for child...\n");
        wait(NULL);
        
        printf("Parent collected child's exit status, zombie is gone\n");
        printf("Parent process terminating\n");
    }
    
    return 0;
}
```

#### Output

Running the program and monitoring the process states:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro labs % ./task6_short & TASK6_PID=$!; echo "Running task6_short with PID $TASK6_PID"; sleep 2; echo -e "\nChecking process state after child terminates (Z = zombie):"; ps -eo pid,ppid,state,comm | grep -E "$TASK6_PID|Z"
[1] 39783
Running task6_short with PID 39783
Parent process (PID: 39783) created child (PID: 39785)
Parent sleeping for 10 seconds while child becomes zombie...
Child process (PID: 39785) started and will terminate shortly
Parent process (PID: 39783) will continue running

Checking process state after child terminates (Z = zombie):
39783 37989 SN   ./task6_short
39785 39783 ZN   <defunct>
```

Then, after the parent calls `wait()`:

```
Parent process now waiting for child...
Parent collected child's exit status, zombie is gone
Parent process terminating
[1]  + done       ./task6_short

Checking process state after parent waits:
```

**Observation:** The output demonstrates the creation of a zombie process. When a child process terminates before its parent collects its exit status, it enters a "zombie" state (shown as 'Z' in the process state). This is seen in the line `39785 39783 ZN <defunct>` where:
- 39785 is the PID of the child process
- 39783 is the PID of the parent process
- Z is the state code indicating a zombie process
- N is the nice value
- `<defunct>` indicates that the process has terminated but its exit status hasn't been collected

After the parent process calls `wait()`, it collects the child's exit status, and the zombie process is removed from the process table, as confirmed by the absence of the zombie process in the final check.

## Supporting Linux Commands for Process Monitoring and Control

• `ps -u username`
• `nice -n 10 ./mycommand`
• `renice -n -10 -p <PID>`
• `nice -n 10 grep "pattern" filename &`
• `ps -l`, `ps -ef`, `ps -au`, `ps -x`, `ps -f`, `ps -ax`, `pstree`
• `grep "main" /usr/include/stdio.h > /Desktop/grep_output.txt`  
  (Search for "main" in system header file and store on Desktop)
• `grep "root" /etc/passwd > /Documents/users.txt`  
  (Find all lines with "root" in user database file)
• `grep "error" /var/log/syslog > /Documents/errors.txt`  
  (Logs all error lines from syslog to a file)
• `grep -i "usb" /var/log/kern.log > /usb_log.txt`  
  (Case-insensitive search for USB-related kernel logs)

## Conclusion

This lab enhances understanding of process creation, execution, and control in Linux. It covers practical applications of `fork()`, `exec()`, `wait()`, and process priority management using `nice` and `renice`. By the end of this lab, students will be able to write concurrent programs, manage process scheduling and use system utilities to analyze process information. 

**Note:** Use appropriate paths if Documents and Desktop don't exist in your file system.

---
**Central Asian University, Tashkent SP25 System Programming**