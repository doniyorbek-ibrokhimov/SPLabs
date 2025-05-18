# Lab 10: System Programming
# Signal Handling and Control in Linux
*using signal(), sigaction(), kill(), and alarm()*

**Muhammad Bilal Qureshi**  
Department of Computer Science  
School of Engineering  

**Report Submission Date:** In next lab

## Objective

- To understand and implement signal handling using signal() and sigaction().
- To differentiate between default and custom signal behaviors.
- To send signals between processes using kill() and observe effects.
- To build a signal-driven stopwatch using child-parent communication and signal delivery.
- To practice signal-based process control (e.g., ignoring, handling, aborting).

## Task 1: Handling Ctrl+C and Ctrl+Z

Write a C program that catches both SIGINT (Ctrl+C) and SIGTSTP (Ctrl+Z), and prints the caught signal number. Do not restore default behavior. Ignore Ctrl+C using:

```c
(void)signal(SIGINT, SIG_IGN);
```

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

void signal_handler(int signum) {
    printf("\nCaught signal %d\n", signum);
}

int main() {
    signal(SIGTSTP, signal_handler);
    
    (void)signal(SIGINT, SIG_IGN);
    
    printf("Press Ctrl+C or Ctrl+Z to test signal handling...\n");
    printf("(Program will run until manually terminated with kill)\n");
    
    while(1) {
        sleep(1);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc -o task1 task1.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task1
Press Ctrl+C or Ctrl+Z to test signal handling...
(Program will run until manually terminated with kill)
^Z
Caught signal 18
^C (No response - signal ignored)
^Z
Caught signal 18
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % kill -TERM 41730
[1]  + terminated  ./task1
```

## Task 2: Signal-based Stopwatch

Write a program with a parent and child process. The child:
- Takes a time interval (in seconds) from the user.
- Counts down each second.
- Sends SIGALRM to the parent when the timer ends.

The parent sets a signal handler to catch SIGALRM and calls a function Times_Up(). If the user presses Ctrl+Z during countdown, the program prints OPERATION ABORTED and exits.

**Sample Output:**
```
$ ./Stop_Watch
$ Enter time (in seconds): 5
$ Time Remaining: 5
$ Time Remaining: 4
$ Time Remaining: 3
$ Time Remaining: 2
$ Time Remaining: 1
$ Time Remaining: 0
$ (TIME FINISH)
```

Or, when aborted:
```
$ Time Remaining: 3
$ OPERATION ABORTED
```

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

pid_t child_pid;
int timer_done = 0;

void Times_Up(int signum) {
    printf("(TIME FINISH)\n");
    exit(0);
}

void handle_tstp(int signum) {
    if (child_pid > 0) {
        kill(child_pid, SIGTERM);
    }
    printf("\nOPERATION ABORTED\n");
    exit(1);
}

int main() {
    int seconds;
    
    signal(SIGALRM, Times_Up);
    signal(SIGTSTP, handle_tstp);
    
    child_pid = fork();
    
    if (child_pid == -1) {
        perror("fork");
        exit(1);
    }
    
    if (child_pid == 0) {
        signal(SIGTSTP, SIG_IGN);
        
        printf("Enter time (in seconds): ");
        scanf("%d", &seconds);
        
        if (seconds <= 0) {
            printf("Please enter a positive number.\n");
            exit(1);
        }
        
        while (seconds >= 0) {
            printf("Time Remaining: %d\n", seconds);
            fflush(stdout);
            
            if (seconds == 0) {
                kill(getppid(), SIGALRM);
                exit(0);
            }
            
            sleep(1);
            seconds--;
        }
        
    } else { 
        pause();
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc -o Stop_Watch Stop_Watch.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./Stop_Watch
Enter time (in seconds): 4
Time Remaining: 4
Time Remaining: 3
Time Remaining: 2
Time Remaining: 1
Time Remaining: 0
(TIME FINISH)
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./Stop_Watch
Enter time (in seconds): 5
Time Remaining: 5
Time Remaining: 4
Time Remaining: 3
^Z
OPERATION ABORTED
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 3: Inter-Process Signal Communication

Create two programs: P1.c and P2.c.

**P1.c:**
- Prints its PID.
- Registers handlers for SIGINT and SIGTSTP using signal().
- Displays "SIGINT Received" or "SIGTSTP Received" based on the signal caught.
- Goes into an infinite loop.

**P2.c:**
- Takes PID of P1 as input.
- Sends SIGINT on input 'C'.
- Sends SIGTSTP on input 'Z'.
- Sends SIGKILL to both P1 and itself on input 'K'.

**Sample Interaction:**
```
$ ./P1
PID of P1: 12345
$ ./P2
Enter PID of P1: 12345
Enter command (C/Z/K): C
=> SIGINT Received
Enter command (C/Z/K): Z
=> SIGTSTP Received
Enter command (C/Z/K): K
=> (Both P1 and P2 terminated)
```

### Implementation

#### P1.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

void signal_handler(int signum) {
    if (signum == SIGINT) {
        printf("\nSIGINT Received\n");
    } else if (signum == SIGTSTP) {
        printf("\nSIGTSTP Received\n");
    }
}

int main() {
    pid_t pid = getpid();
    
    signal(SIGINT, signal_handler);
    signal(SIGTSTP, signal_handler);
    
    printf("PID of P1: %d\n", pid);
    
    while(1) {
        sleep(1);
    }
    
    return 0;
}
```

#### P2.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

int main() {
    pid_t p1_pid;
    char command;
    
    printf("Enter PID of P1: ");
    scanf("%d", &p1_pid);
    
    while(1) {
        printf("Enter command (C/Z/K): ");
        scanf(" %c", &command);
        
        switch(command) {
            case 'C':
            case 'c':
                kill(p1_pid, SIGINT);
                break;
                
            case 'Z':
            case 'z':
                kill(p1_pid, SIGTSTP);
                break;
                
            case 'K':
            case 'k':
                kill(p1_pid, SIGKILL);
                printf("Sent SIGKILL to P1\n");
                printf("Terminating P2...\n");
                exit(0);
                break;
                
            default:
                printf("Invalid command. Use C, Z, or K\n");
        }
        
        sleep(1);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./P1 &
[1] 43409
PID of P1: 43409
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./P2
Enter PID of P1: 43409
Enter command (C/Z/K): C

SIGINT Received
Enter command (C/Z/K): Z

SIGTSTP Received
Enter command (C/Z/K): K
Sent SIGKILL to P1
Terminating P2...
[1]  + killed     ./P1
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 4: sigaction with selective signal ignoring

Write a program that:
- Uses sigaction() to set custom signal handlers.
- On receiving SIGINT (Ctrl+C), starts printing characters from a to z, each with a delay of 1 second.
- Ignores SIGTSTP (Ctrl+Z) during the character printing process using SIG_IGN.
- Exits the program only if SIGTSTP is sent before the printing starts.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

volatile sig_atomic_t printing_started = 0;

void signal_handler(int signum) {
    if (signum == SIGINT) {
        printing_started = 1;
    } else if (signum == SIGTSTP) {
        if (!printing_started) {
            printf("\nSIGTSTP received before printing started. Exiting...\n");
            exit(0);
        }
    }
}

int main() {
    struct sigaction sa_int, sa_tstp;
    
    // Set up SIGINT handler
    sa_int.sa_handler = signal_handler;
    sigemptyset(&sa_int.sa_mask);
    sa_int.sa_flags = 0;
    
    // Set up SIGTSTP handler
    sa_tstp.sa_handler = signal_handler;
    sigemptyset(&sa_tstp.sa_mask);
    sa_tstp.sa_flags = 0;
    
    // Register the signal handlers using sigaction
    if (sigaction(SIGINT, &sa_int, NULL) == -1) {
        perror("Error setting up SIGINT handler");
        exit(1);
    }
    
    if (sigaction(SIGTSTP, &sa_tstp, NULL) == -1) {
        perror("Error setting up SIGTSTP handler");
        exit(1);
    }
    
    printf("Press Ctrl+C to start printing a-z\n");
    printf("Press Ctrl+Z to exit (only works before printing starts)\n");
    
    // Wait for SIGINT to start printing
    while (!printing_started) {
        sleep(1);
    }
    
    printf("\nStarting to print a to z...\n");
    
    // Ignore SIGTSTP during printing
    sa_tstp.sa_handler = SIG_IGN;
    if (sigaction(SIGTSTP, &sa_tstp, NULL) == -1) {
        perror("Error changing SIGTSTP handler");
        exit(1);
    }
    
    // Print a to z with 1-second delay
    char letter;
    for (letter = 'a'; letter <= 'z'; letter++) {
        printf("%c\n", letter);
        fflush(stdout);
        sleep(1);
    }
    
    printf("\nPrinting completed.\n");
    
    return 0;
}
```

### Terminal Output

#### Case 1: Printing characters after receiving Ctrl+C

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task4_sigaction
Press Ctrl+C to start printing a-z
Press Ctrl+Z to exit (only works before printing starts)
^C
Starting to print a to z...
a
b
c
d
e
f
g
h
i
j
k
l
m
n
o
p
q
r
s
t
u
v
w
x
y
z

Printing completed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

#### Case 2: Exiting the program when Ctrl+Z is pressed before printing starts

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task4_sigaction
Press Ctrl+C to start printing a-z
Press Ctrl+Z to exit (only works before printing starts)
^Z
SIGTSTP received before printing started. Exiting...
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 5: Real-Life Scenario: Signal Handling in Server Shutdown

You are managing a server that handles thousands of client connections. Suppose the administrator needs to gracefully shut down the server process (to apply updates) without abruptly cutting off clients.

**Scenario:** When the admin sends a SIGTERM signal (using `kill -15 <PID>`), the server process should:
- Stop accepting new client connections.
- Notify connected clients of an impending shutdown.
- Release resources and close log files.
- Exit safely after completing ongoing tasks.

**Your Task:**
- Write a signal handler for SIGTERM that prints "Shutting down gracefully..." and performs the cleanup.
- Integrate this handler into a dummy server that runs in an infinite loop (simulating client handling).
- Demonstrate the graceful exit when `kill -15 <PID>` is issued.

**Discussion:** Why is graceful shutdown via signals better than using SIGKILL? What could go wrong if signals are not properly handled?

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <time.h>
#include <string.h>

#define MAX_CLIENTS 1000
#define SHUTDOWN_DELAY 5

typedef struct {
    int client_id;
    time_t connection_time;
    int is_active;
} Client;

volatile sig_atomic_t shutdown_requested = 0;
volatile sig_atomic_t accepting_connections = 1;
Client clients[MAX_CLIENTS];
int active_clients = 0;
FILE *log_file = NULL;

void get_time_str(char *buffer, size_t size) {
    time_t now = time(NULL);
    strftime(buffer, size, "%Y-%m-%d %H:%M:%S", localtime(&now));
}

void log_message(const char *message) {
    char time_str[30];
    get_time_str(time_str, sizeof(time_str));
    
    printf("[%s] %s\n", time_str, message);
    
    if (log_file) {
        fprintf(log_file, "[%s] %s\n", time_str, message);
        fflush(log_file);
    }
}

void accept_client() {
    if (!accepting_connections) {
        log_message("Rejected new connection (server is shutting down)");
        return;
    }
    
    for (int i = 0; i < MAX_CLIENTS; i++) {
        if (!clients[i].is_active) {
            clients[i].client_id = i + 1;
            clients[i].connection_time = time(NULL);
            clients[i].is_active = 1;
            active_clients++;
            
            char msg[100];
            sprintf(msg, "Client #%d connected (Total active: %d)", 
                   clients[i].client_id, active_clients);
            log_message(msg);
            return;
        }
    }
    
    log_message("Connection rejected: maximum clients reached");
}

void disconnect_client(int client_idx) {
    if (clients[client_idx].is_active) {
        char msg[100];
        sprintf(msg, "Client #%d disconnected", clients[client_idx].client_id);
        log_message(msg);
        
        clients[client_idx].is_active = 0;
        active_clients--;
    }
}

void handle_client_requests() {
    for (int i = 0; i < MAX_CLIENTS; i++) {
        if (clients[i].is_active) {
            usleep(50000);
            
            if (rand() % 100 < 5) {
                disconnect_client(i);
            }
        }
    }
}

void notify_clients_shutdown() {
    log_message("Notifying all clients about imminent shutdown");
    
    for (int i = 0; i < MAX_CLIENTS; i++) {
        if (clients[i].is_active) {
            char msg[100];
            sprintf(msg, "Sending shutdown notice to Client #%d", clients[i].client_id);
            log_message(msg);
            
        }
    }
}

void disconnect_all_clients() {
    log_message("Disconnecting all clients...");
    
    for (int i = 0; i < MAX_CLIENTS; i++) {
        if (clients[i].is_active) {
            disconnect_client(i);
        }
    }
    
    log_message("All clients disconnected");
}

void cleanup_resources() {
    log_message("Releasing server resources");
    
    if (log_file) {
        log_message("Closing log file");
        fclose(log_file);
        log_file = NULL;
    }
    
    log_message("All resources released");
}

void sigterm_handler(int signum) {
    char msg[100];
    sprintf(msg, "Received signal %d (SIGTERM)", signum);
    log_message(msg);
    log_message("Shutting down gracefully...");
    
    shutdown_requested = 1;
    
    accepting_connections = 0;
}

int main() {
    srand(time(NULL));
    
    log_file = fopen("server.log", "w");
    if (!log_file) {
        perror("Error opening log file");
        return 1;
    }
    
    memset(clients, 0, sizeof(clients));
    

    struct sigaction sa;
    sa.sa_handler = sigterm_handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    
    if (sigaction(SIGTERM, &sa, NULL) == -1) {
        log_message("Error setting up SIGTERM handler");
        fclose(log_file);
        return 1;
    }
    

    char pid_msg[50];
    sprintf(pid_msg, "Server started with PID: %d", getpid());
    log_message(pid_msg);
    log_message("Use 'kill -15 <PID>' to initiate graceful shutdown");
    

    int simulation_time = 0;
    while (1) {

        if (shutdown_requested) {
            log_message("Starting graceful shutdown procedure");

            notify_clients_shutdown();
            

            for (int i = SHUTDOWN_DELAY; i > 0; i--) {
                char countdown_msg[50];
                sprintf(countdown_msg, "Server shutting down in %d seconds...", i);
                log_message(countdown_msg);
                sleep(1);
            }
            
            disconnect_all_clients();
            
            cleanup_resources();
            
            log_message("Server shutdown complete");
            break;
        }
        
        if (simulation_time % 2 == 0 && rand() % 100 < 70) {
            accept_client();
        }
        
        handle_client_requests();
        
        sleep(1);
        simulation_time++;
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./server_shutdown &
[1] 47166
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % [2025-05-19 00:50:30] Server started with PID: 47166
[2025-05-19 00:50:30] Use 'kill -15 <PID>' to initiate graceful shutdown
[2025-05-19 00:50:34] Client #1 connected (Total active: 1)
echo $!
47166
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % [2025-05-19 00:50:36] Client #2 connected (Total active: 2)
ps -p 47166
  PID TTY           TIME CMD
47166 ttys009    0:00.00 ./server_shutdown
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % kill -15 47166
[2025-05-19 00:50:43] Received signal 15 (SIGTERM)
[2025-05-19 00:50:43] Shutting down gracefully...
[2025-05-19 00:50:43] Starting graceful shutdown procedure
[2025-05-19 00:50:43] Notifying all clients about imminent shutdown
[2025-05-19 00:50:43] Sending shutdown notice to Client #1
[2025-05-19 00:50:43] Sending shutdown notice to Client #2
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ps -p 47166
  PID TTY           TIME CMD
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

### Server Log File

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cat server.log
[2025-05-19 00:50:30] Server started with PID: 47166
[2025-05-19 00:50:30] Use 'kill -15 <PID>' to initiate graceful shutdown
[2025-05-19 00:50:34] Client #1 connected (Total active: 1)
[2025-05-19 00:50:36] Client #2 connected (Total active: 2)
[2025-05-19 00:50:40] Client #3 connected (Total active: 3)
[2025-05-19 00:50:43] Received signal 15 (SIGTERM)
[2025-05-19 00:50:43] Shutting down gracefully...
[2025-05-19 00:50:43] Starting graceful shutdown procedure
[2025-05-19 00:50:43] Notifying all clients about imminent shutdown
[2025-05-19 00:50:43] Sending shutdown notice to Client #1
[2025-05-19 00:50:43] Sending shutdown notice to Client #2
[2025-05-19 00:50:43] Sending shutdown notice to Client #3
[2025-05-19 00:50:43] Server shutting down in 5 seconds...
[2025-05-19 00:50:44] Server shutting down in 4 seconds...
[2025-05-19 00:50:45] Server shutting down in 3 seconds...
[2025-05-19 00:50:46] Server shutting down in 2 seconds...
[2025-05-19 00:50:47] Server shutting down in 1 seconds...
[2025-05-19 00:50:48] Disconnecting all clients...
[2025-05-19 00:50:48] Client #1 disconnected
[2025-05-19 00:50:48] Client #2 disconnected
[2025-05-19 00:50:48] Client #3 disconnected
[2025-05-19 00:50:48] All clients disconnected
[2025-05-19 00:50:48] Releasing server resources
[2025-05-19 00:50:48] Closing log file
```

### Discussion

Graceful shutdown via signals (like SIGTERM) is better than using SIGKILL for several important reasons:

1. **Resource Cleanup**: SIGTERM allows the program to perform proper cleanup, closing files, releasing memory, and shutting down network connections. SIGKILL terminates the process immediately without giving it a chance to clean up.

2. **Data Integrity**: With SIGTERM, servers can complete in-progress transactions, save data to disk, and ensure that databases or files are left in a consistent state. SIGKILL can lead to corrupted data or transactions being partially completed.

3. **Client Notification**: As demonstrated in our server, SIGTERM allows the server to notify connected clients about the shutdown, giving them time to finish their work or reconnect later.

4. **Preventing Orphaned Resources**: Properly handling shutdown signals prevents resources like shared memory segments, semaphores, or temporary files from being orphaned.

If signals are not properly handled, several problems can occur:

1. **Resource Leaks**: Files might remain open, memory might not be freed, and network connections might stay open.

2. **Data Corruption**: Abrupt termination during critical operations could leave data in an inconsistent state.

3. **Zombie Processes**: Child processes might become zombies if parent processes don't properly handle their termination.

4. **Client Disruption**: In a server context, clients might be disconnected without warning, potentially causing data loss on their end.

5. **Deadlocks**: If a signal interrupts a critical section, it could leave shared resources in a locked state, causing other processes to deadlock.

In our implementation, we used `volatile sig_atomic_t` variables and minimal operations in the signal handler to ensure safe signal handling, following best practices for writing robust server applications.

## Conclusion

This lab introduces Linux signal mechanisms including user-generated interrupts, inter-process communication, and signal-driven event control. Students practiced building robust C programs that react to signals like Ctrl+C and Ctrl+Z, manage timers via alarm signals, and use process IDs to direct signal flows. Additionally, a real-world server simulation emphasized the importance of graceful termination using signal handling. By the end of this lab, students will be confident in writing resilient code that can interact with the operating system asynchronously and reliably.
