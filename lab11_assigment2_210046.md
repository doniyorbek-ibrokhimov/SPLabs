# Lab 11: System Programming
# Inter-Process Communication and Pipes

**Muhammad Bilal Qureshi**  
Department of Computer Science  
School of Engineering  

**Report Submission Date:** In next lab

**Note:** Q1 to Q5 are required to be performed in the lab and are part of Lab 11. Task 1 to Task 6 is a take-home assignment (Assignment #2) and must be uploaded on Eduplus on or before 1st May, 2025. Quiz 2 will take place in Monday and Tuesday lecture respectively.

## Objective

- Understand how to use popen() for reading/writing to external programs.
- Implement single and multi-process communication using unnamed pipes.
- Implement producer-consumer behavior using inter-process communication.
- Learn to use named pipes (FIFOs) for IPC across different terminals.
- Practice reading, writing, and transforming data between multiple processes.

## Q1: Reading from External Program using popen()

Run popen1.c and understand how to take output from an external program like ls or find. Store it in a buffer and print it. [lecture # 20]

### Implementation

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main() {
    FILE *read_fp;
    char buffer[BUFSIZ + 1]; 
    int chars_read;
    
    memset(buffer, '\0', sizeof(buffer));
    
    // Example 1: Using ls command to list files
    printf("Running 'ls -la' command...\n");
    read_fp = popen("ls -la", "r");
    if (read_fp != NULL) {
        chars_read = fread(buffer, sizeof(char), BUFSIZ, read_fp);
        if (chars_read > 0) {
            printf("Output of ls -la command:\n%s\n", buffer);
        }
        pclose(read_fp);
    } else {
        fprintf(stderr, "popen failed for ls command\n");
        exit(EXIT_FAILURE);
    }
    
    // Example 2: Using find command to search for files
    memset(buffer, '\0', sizeof(buffer));
    printf("\nRunning 'find . -type f -name \"*.c\"' command...\n");
    read_fp = popen("find . -type f -name \"*.c\"", "r");
    if (read_fp != NULL) {
        chars_read = fread(buffer, sizeof(char), BUFSIZ, read_fp);
        if (chars_read > 0) {
            printf("Output of find command:\n%s\n", buffer);
        }
        pclose(read_fp);
    } else {
        fprintf(stderr, "popen failed for find command\n");
        exit(EXIT_FAILURE);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/lab_resource_files/Lab11/Q1 && gcc -o popen1 popen1.c && ./popen1
Running 'ls -la' command...
Output of ls -la command:
total 80
drwxr-xr-x@  4 d.ibrokhimov  staff    128 May 19 02:36 .
drwxr-xr-x@ 10 d.ibrokhimov  staff    320 May 19 02:35 ..
-rwxr-xr-x@  1 d.ibrokhimov  staff  33848 May 19 02:36 popen1
-rw-r--r--@  1 d.ibrokhimov  staff   1250 May 19 02:36 popen1.c


Running 'find . -type f -name "*.c"' command...
Output of find command:
./popen1.c
```

## Q2: Writing to External Program using popen()

Run popen2.c to learn how to write input to an external program like sort or wc. Observe how data is processed and returned. [lecture # 20]

### Implementation

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main() {
    FILE *write_fp;
    char buffer[BUFSIZ + 1];
    
    // Example 1: Sending data to sort command
    printf("Example 1: Sending data to 'sort' command...\n");
    write_fp = popen("sort", "w");
    if (write_fp != NULL) {
        fputs("zebra\n", write_fp);
        fputs("apple\n", write_fp);
        fputs("orange\n", write_fp);
        fputs("banana\n", write_fp);
        
        printf("Data sent to sort: zebra, apple, orange, banana\n");
        printf("Sorted output (after closing pipe):\n");
        
        if (pclose(write_fp) != 0) {
            fprintf(stderr, "Error closing pipe to sort\n");
            exit(EXIT_FAILURE);
        }
    } else {
        fprintf(stderr, "popen failed for sort command\n");
        exit(EXIT_FAILURE);
    }
    
    // Example 2: Sending data to wc command to count lines, words, and chars
    printf("\nExample 2: Sending data to 'wc' command...\n");
    write_fp = popen("wc", "w");
    if (write_fp != NULL) {
        const char *text = "This is line one.\nThis is line two.\nAnd this is line three.\n";
        
        fwrite(text, sizeof(char), strlen(text), write_fp);
        
        printf("Data sent to wc:\n%s", text);
        printf("Word count result (after closing pipe):\n");
        
        if (pclose(write_fp) != 0) {
            fprintf(stderr, "Error closing pipe to wc\n");
            exit(EXIT_FAILURE);
        }
    } else {
        fprintf(stderr, "popen failed for wc command\n");
        exit(EXIT_FAILURE);
    }
    
    // Example 3: Using od -c to display character representation
    printf("\nExample 3: Sending data to 'od -c' command...\n");
    write_fp = popen("od -c", "w");
    if (write_fp != NULL) {
        const char *text = "Hello, popen! This demonstrates writing to a command.";
        
        fwrite(text, sizeof(char), strlen(text), write_fp);
        
        printf("Data sent to od -c: \"%s\"\n", text);
        printf("Octal dump output (after closing pipe):\n");
        
        if (pclose(write_fp) != 0) {
            fprintf(stderr, "Error closing pipe to od\n");
            exit(EXIT_FAILURE);
        }
    } else {
        fprintf(stderr, "popen failed for od command\n");
        exit(EXIT_FAILURE);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Q2 % gcc -o popen2 popen2.c && ./popen2
Example 1: Sending data to 'sort' command...
Data sent to sort: zebra, apple, orange, banana
Sorted output (after closing pipe):
apple
banana
orange
zebra

Example 2: Sending data to 'wc' command...
Data sent to wc:
This is line one.
This is line two.
And this is line three.
Word count result (after closing pipe):
       3      13      60

Example 3: Sending data to 'od -c' command...
Data sent to od -c: "Hello, popen! This demonstrates writing to a command."
Octal dump output (after closing pipe):
0000000    H   e   l   l   o   ,       p   o   p   e   n   !       T   h
0000020    i   s       d   e   m   o   n   s   t   r   a   t   e   s    
0000040    w   r   i   t   i   n   g       t   o       a       c   o   m
0000060    m   a   n   d   .                                            
0000065
```

## Q3: Single Process Communication using Pipe

Run pipe2.c to understand the working of pipe(), write(), and read() within a single process using fork. [lecture # 20]

### Implementation

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>

int main() {
    int data_processed;
    int file_pipes[2];
    char buffer[BUFSIZ + 1];
    pid_t fork_result;
    const char some_data[] = "Hello from parent process!";
    
    memset(buffer, '\0', sizeof(buffer));
    
    if (pipe(file_pipes) == 0) {
        fork_result = fork();
        
        if (fork_result == -1) {
            fprintf(stderr, "Fork failure");
            exit(EXIT_FAILURE);
        }
        
        if (fork_result == 0) {
            // Child process
            close(file_pipes[1]);  // Close unused write end
            
            data_processed = read(file_pipes[0], buffer, BUFSIZ);
            
            printf("Child process (PID %d) - Read %d bytes: %s\n", getpid(), data_processed, buffer);
            
            close(file_pipes[0]);  // Close read end
            exit(EXIT_SUCCESS);
        }
        else {
            // Parent process
            close(file_pipes[0]);  // Close unused read end
            
            data_processed = write(file_pipes[1], some_data, strlen(some_data));
            
            printf("Parent process (PID %d) - Wrote %d bytes to pipe\n", getpid(), data_processed);
            
            close(file_pipes[1]);  // Close write end
            
            printf("Parent process - Waiting for child to complete...\n");
            
            wait(NULL);
            
            printf("Parent process - Child completed\n");
        }
    }
    else {
        fprintf(stderr, "Pipe failed");
        exit(EXIT_FAILURE);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Q3 % gcc -o pipe2 pipe2.c && ./pipe2
Parent process (PID 55349) - Wrote 26 bytes to pipe
Parent process - Waiting for child to complete...
Child process (PID 55360) - Read 26 bytes: Hello from parent process!
Parent process - Child completed
```

## Q4: Multi-Process Pipe Communication

Run pipe2.c to explore how a parent process creates two child processes and enables communication through pipes.

### Implementation

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>

int main() {
    int pipe1[2];
    int pipe2[2];
    pid_t child1, child2;
    char buffer[BUFSIZ + 1];
    int data_processed;

    if (pipe(pipe1) == -1 || pipe(pipe2) == -1) {
        fprintf(stderr, "Pipe creation failed\n");
        exit(EXIT_FAILURE);
    }

    child1 = fork();
    if (child1 == -1) {
        fprintf(stderr, "Fork for Child 1 failed\n");
        exit(EXIT_FAILURE);
    }

    if (child1 == 0) {
        close(pipe1[1]);
        close(pipe2[0]);

        memset(buffer, 0, sizeof(buffer));
        data_processed = read(pipe1[0], buffer, BUFSIZ);
        printf("Child 1 (PID %d): Received from parent: %s\n", getpid(), buffer);

        char new_message[BUFSIZ + 1];
        snprintf(new_message, BUFSIZ, "Message from Child 1 (modified parent message): %s", buffer);
        
        data_processed = write(pipe2[1], new_message, strlen(new_message) + 1);
        printf("Child 1 (PID %d): Sent %d bytes to Child 2\n", getpid(), data_processed);

        close(pipe1[0]);
        close(pipe2[1]);
        
        exit(EXIT_SUCCESS);
    }
    else {
        child2 = fork();
        if (child2 == -1) {
            fprintf(stderr, "Fork for Child 2 failed\n");
            exit(EXIT_FAILURE);
        }

        if (child2 == 0) {
            close(pipe1[0]);
            close(pipe1[1]);
            close(pipe2[1]);

            memset(buffer, 0, sizeof(buffer));
            data_processed = read(pipe2[0], buffer, BUFSIZ);
            printf("Child 2 (PID %d): Received from Child 1: %s\n", getpid(), buffer);

            close(pipe2[0]);
            
            exit(EXIT_SUCCESS);
        }
        else {
            close(pipe1[0]);
            close(pipe2[0]);
            close(pipe2[1]);

            const char* message = "Hello from parent to child processes!";
            data_processed = write(pipe1[1], message, strlen(message) + 1);
            printf("Parent (PID %d): Sent %d bytes to Child 1\n", getpid(), data_processed);

            close(pipe1[1]);

            printf("Parent: Waiting for children to complete...\n");
            wait(NULL);
            wait(NULL);
            printf("Parent: All children have completed\n");
        }
    }

    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Q4 % gcc -o pipe_multi pipe_multi.c && ./pipe_multi
Child 1 (PID 55672): Received from parent: Hello from parent to child processes!
Child 1 (PID 55672): Sent 86 bytes to Child 2
Parent (PID 55668): Sent 38 bytes to Child 1
Parent: Waiting for children to complete...
Child 2 (PID 55673): Received from Child 1: Message from Child 1 (modified parent message): Hello from parent to child processes!
Parent: All children have completed
```

## Q5: Pipe between Different Programs

Run pipe3.c to simulate communication between two different programs (e.g., producer and consumer) using pipe. [lecture # 21]

### Implementation of pipe3.c

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>

int main() {
    int data_processed;
    int file_pipes[2];
    const char some_data[] = "123";
    char buffer[BUFSIZ + 1];
    pid_t fork_result;
    
    memset(buffer, '\0', sizeof(buffer));
    
    if (pipe(file_pipes) == 0) {
        fork_result = fork();
        
        if (fork_result == -1) {
            fprintf(stderr, "Fork failure");
            exit(EXIT_FAILURE);
        }
        
        if (fork_result == 0) {
            char read_fd_str[10];
            
            sprintf(read_fd_str, "%d", file_pipes[0]);
            
            execl("./pipe_reader", "pipe_reader", read_fd_str, (char *)0);
            
            fprintf(stderr, "Could not exec pipe_reader");
            exit(EXIT_FAILURE);
        }
        else {
            data_processed = write(file_pipes[1], some_data, strlen(some_data));
            printf("Parent (PID %d): Wrote %d bytes: \"%s\"\n", getpid(), data_processed, some_data);
            
            wait(NULL);
        }
    }
    
    exit(EXIT_SUCCESS);
}
```

### Implementation of pipe_reader.c

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    int data_processed;
    char buffer[BUFSIZ + 1];
    int file_descriptor;
    
    memset(buffer, '\0', sizeof(buffer));
    
    if (argc < 2) {
        fprintf(stderr, "Usage: %s file_descriptor\n", argv[0]);
        exit(EXIT_FAILURE);
    }
    
    sscanf(argv[1], "%d", &file_descriptor);
    data_processed = read(file_descriptor, buffer, BUFSIZ);
    
    printf("Child (pipe_reader, PID %d): Read %d bytes: \"%s\"\n", 
           getpid(), data_processed, buffer);
    
    exit(EXIT_SUCCESS);
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Q5 % gcc -o pipe_reader pipe_reader.c && gcc -o pipe3 pipe3.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Q5 % ./pipe3
Parent (PID 56199): Wrote 3 bytes: "123"
Child (pipe_reader, PID 56202): Read 3 bytes: "123"
```

## Task 1: Two-Child Communication using Pipe

Write a C program where a parent process creates two child processes:
- Child 1 sends a message to Child 2 using a pipe.
- Child 2 receives the message and prints it.
- Implement proper error handling and resource management.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    int pipe_fd[2];
    pid_t child1, child2;
    const char message[] = "Hello from Child 1!";
    char buffer[100];
    
    if (pipe(pipe_fd) == -1) {
        perror("Pipe creation failed");
        exit(EXIT_FAILURE);
    }
    
    child1 = fork();
    if (child1 < 0) {
        perror("Fork failed for first child");
        exit(EXIT_FAILURE);
    }
    
    if (child1 == 0) {
        close(pipe_fd[0]);
        
        printf("Child 1 (PID %d): Sending message to Child 2\n", getpid());
        
        write(pipe_fd[1], message, strlen(message) + 1);
        
        close(pipe_fd[1]);
        
        exit(EXIT_SUCCESS);
    } else {
        child2 = fork();
        
        if (child2 < 0) {
            perror("Fork failed for second child");
            exit(EXIT_FAILURE);
        }
        
        if (child2 == 0) {
            close(pipe_fd[1]);
            
            memset(buffer, 0, sizeof(buffer));
            
            int bytes_read = read(pipe_fd[0], buffer, sizeof(buffer));
            
            if (bytes_read > 0) {
                printf("Child 2 (PID %d): Received message: %s\n", getpid(), buffer);
            } else {
                printf("Child 2 (PID %d): No message received\n", getpid());
            }
            
            close(pipe_fd[0]);
            
            exit(EXIT_SUCCESS);
        } else {
            close(pipe_fd[0]);
            close(pipe_fd[1]);
            
            printf("Parent (PID %d): Created Child 1 (PID %d) and Child 2 (PID %d)\n", 
                   getpid(), child1, child2);
            
            wait(NULL);
            wait(NULL);
            
            printf("Parent: Both children have completed\n");
        }
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc -o task1 task1.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task1
Child 1 (PID 42884): Sending message to Child 2
Parent (PID 42877): Created Child 1 (PID 42884) and Child 2 (PID 42885)
Child 2 (PID 42885): Received message: Hello from Child 1!
Parent: Both children have completed
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 2: popen with fread and fwrite

Create a C program that performs the following:
- Use popen() with command:
  ```c
  char command[] = "find . -type f -name \"*.txt\"";
  ```
- Store output in a buffer using fread().
- Change the command to:
  ```c
  char command[] = "cat abc.txt | wc -l && cat abc.txt | wc -c";
  ```
- Store result and display character and line count.
- Finally use popen() with od -c to display output in octal format.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

int main() {
    FILE *read_fp;
    char buffer[BUFSIZ + 1];
    int chars_read;
    
    memset(buffer, '\0', sizeof(buffer));
    read_fp = popen("find . -type f -name \"*.txt\"", "r");
    if (read_fp != NULL) {
        chars_read = fread(buffer, sizeof(char), BUFSIZ, read_fp);
        if (chars_read > 0) {
            printf("Text files found:\n%s\n", buffer);
        }
        pclose(read_fp);
    } else {
        fprintf(stderr, "popen failed for find command\n");
        exit(EXIT_FAILURE);
    }
    
    FILE *abc_file = fopen("abc.txt", "w");
    if (abc_file != NULL) {
        fprintf(abc_file, "Line 1\nLine 2\nLine 3\nLine 4\nLine 5\n");
        fclose(abc_file);
        printf("Created sample abc.txt file\n");
    } else {
        fprintf(stderr, "Failed to create abc.txt file\n");
    }
    
    memset(buffer, '\0', sizeof(buffer));
    read_fp = popen("cat abc.txt | wc -l && cat abc.txt | wc -c", "r");
    if (read_fp != NULL) {
        chars_read = fread(buffer, sizeof(char), BUFSIZ, read_fp);
        if (chars_read > 0) {
            printf("Line and character count of abc.txt:\n%s\n", buffer);
        }
        pclose(read_fp);
    } else {
        fprintf(stderr, "popen failed for wc command\n");
        exit(EXIT_FAILURE);
    }
    
    memset(buffer, '\0', sizeof(buffer));
    read_fp = popen("od -c abc.txt", "r");
    if (read_fp != NULL) {
        chars_read = fread(buffer, sizeof(char), BUFSIZ, read_fp);
        if (chars_read > 0) {
            printf("Octal dump of abc.txt:\n%s\n", buffer);
        }
        pclose(read_fp);
    } else {
        fprintf(stderr, "popen failed for od command\n");
        exit(EXIT_FAILURE);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc -o task2 task2.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task2
Text files found:
./SP/labs/lsoutput.txt
./SP/labs/Lab8/results2.txt
./SP/labs/Lab8/results3.txt
./SP/labs/Lab8/decrypted_wrong.txt
./SP/labs/Lab8/decrypted_X.txt
./SP/labs/Lab8/results.txt
./SP/labs/Lab8/input.txt
./SP/labs/Lab8/encrypted.txt
./SP/labs/Lab8/encrypted_X.txt
./SP/labs/Lab8/decrypted1.txt
./SP/labs/pid_before_exec.txt
./SP/labs/Lab6/output1.txt
./SP/labs/Lab6/metadata.txt
./SP/labs/Lab6/bad_data.txt
./SP/labs/Lab6/data.txt
./SP/labs/Lab6/sample.txt
./SP/labs/mydata.txt
./SP/labs/killerr.txt
./SP/labs/task6_output.txt
./SP/labs/man_bash_output.txt
./SP/labs/Lab5/lab_date.txt
./SP/labs/Lab5/copied_file.txt
./SP/labs/Lab5/large_test.txt
./SP/labs/Lab5/output.txt
./SP/labs/Lab5/sample.txt
./SP/labs/Lab3/sample_file2.txt
./SP/labs/Lab3/sample_file3.txt
./SP/labs/Lab3/sample_file1.txt
./SP/labs/Lab3/dummy_dir_for_rm/test_in_dummy.txt
./SP/labs/Lab3/uniq_sample.txt
./SP/labs/Lab3/monitored_dir/new_test_file.txt
./SP/labs/Lab4/fileD.txt
./SP/labs/Lab4/fileC.txt
./SP/labs/Lab4/fileA.txt
./SP/labs/psout.txt
./SP/labs/sour
Created sample abc.txt file
Line and character count of abc.txt:
       5
      35

Octal dump of abc.txt:
0000000    L   i   n   e       1  \n   L   i   n   e       2  \n   L   i
0000020    n   e       3  \n   L   i   n   e       4  \n   L   i   n   e
0000040        5  \n                                                    
0000043

d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 3: Producer-Consumer using Pipe

Write a C program using fork to:
- Create a producer that writes 5 random numbers to a pipe.
- Consumer reads the numbers and calculates the sum.
- Use a loop for writing and reading.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <time.h>
#include <sys/wait.h>

#define NUM_ITEMS 5
#define MAX_RANDOM_NUM 100

int main() {
    int pipe_fd[2];
    pid_t pid;
    int numbers[NUM_ITEMS];
    int sum = 0;
    
    if (pipe(pipe_fd) == -1) {
        perror("Pipe creation failed");
        exit(EXIT_FAILURE);
    }
    
    pid = fork();
    if (pid < 0) {
        perror("Fork failed");
        exit(EXIT_FAILURE);
    }
    
    if (pid > 0) {
        close(pipe_fd[0]);
        
        srand(time(NULL));
        
        printf("Producer: Generating %d random numbers...\n", NUM_ITEMS);
        
        for (int i = 0; i < NUM_ITEMS; i++) {
            numbers[i] = rand() % MAX_RANDOM_NUM + 1;
            printf("Producer: Generated number %d: %d\n", i+1, numbers[i]);
            
            write(pipe_fd[1], &numbers[i], sizeof(int));
            
            usleep(100000);
        }
        
        close(pipe_fd[1]);
        printf("Producer: All numbers sent to the pipe\n");
        
        wait(NULL);
        
    } else {
        close(pipe_fd[1]);
        
        printf("Consumer: Reading numbers and calculating sum...\n");
        
        for (int i = 0; i < NUM_ITEMS; i++) {
            int received_num;
            int bytes_read = read(pipe_fd[0], &received_num, sizeof(int));
            
            if (bytes_read > 0) {
                printf("Consumer: Received number %d: %d\n", i+1, received_num);
                sum += received_num;
            } else {
                printf("Consumer: Error reading from pipe\n");
                close(pipe_fd[0]);
                exit(EXIT_FAILURE);
            }
        }
        
        close(pipe_fd[0]);
        printf("Consumer: Sum of all numbers: %d\n", sum);
        
        exit(EXIT_SUCCESS);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc -o task3 task3.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % ./task3
Producer: Generating 5 random numbers...
Producer: Generated number 1: 69
Consumer: Reading numbers and calculating sum...
Consumer: Received number 1: 69
Producer: Generated number 2: 9
Consumer: Received number 2: 9
Producer: Generated number 3: 22
Consumer: Received number 3: 22
Producer: Generated number 4: 52
Consumer: Received number 4: 52
Producer: Generated number 5: 99
Consumer: Received number 5: 99
Consumer: Sum of all numbers: 251
Producer: All numbers sent to the pipe
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Task 4: Pipe Between Parent and Child

Create a program where:
- Parent sends an array of integers to child using pipe.
- Child calculates and returns the average using pipe.
- Use two pipes for full-duplex communication.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

#define ARRAY_SIZE 5

int main() {
    int parent_to_child[2]; 
    int child_to_parent[2]; 
    pid_t pid;
    int numbers[ARRAY_SIZE] = {25, 30, 15, 40, 10};
    float average;
    
    if (pipe(parent_to_child) == -1 || pipe(child_to_parent) == -1) {
        perror("Pipe creation failed");
        exit(EXIT_FAILURE);
    }
    
    pid = fork();
    if (pid < 0) {
        perror("Fork failed");
        exit(EXIT_FAILURE);
    }
    
    if (pid > 0) {
        // Parent process
        close(parent_to_child[0]);  
        close(child_to_parent[1]);  
        
        printf("Parent: Sending array of integers to child...\n");
        
        int size = ARRAY_SIZE;
        write(parent_to_child[1], &size, sizeof(int));
        
        for (int i = 0; i < ARRAY_SIZE; i++) {
            printf("Parent: Sending number %d: %d\n", i+1, numbers[i]);
            write(parent_to_child[1], &numbers[i], sizeof(int));
        }
        
        close(parent_to_child[1]);
        
        read(child_to_parent[0], &average, sizeof(float));
        printf("Parent: Received average from child: %.2f\n", average);
        
        close(child_to_parent[0]);
        
        wait(NULL);
        
    } else {
        close(parent_to_child[1]);  
        close(child_to_parent[0]);  
        
        printf("Child: Reading integers from parent...\n");
        
        int size;
        read(parent_to_child[0], &size, sizeof(int));
        
        int sum = 0;
        for (int i = 0; i < size; i++) {
            int num;
            read(parent_to_child[0], &num, sizeof(int));
            printf("Child: Received number %d: %d\n", i+1, num);
            sum += num;
        }
        
        close(parent_to_child[0]);
        
        float avg = (float)sum / size;
        printf("Child: Calculated average: %.2f\n", avg);
        
        write(child_to_parent[1], &avg, sizeof(float));
        printf("Child: Sent average to parent\n");
        
        close(child_to_parent[1]);
        
        exit(EXIT_SUCCESS);
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 % gcc -o task4 task4.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 % ./task4
Parent: Sending array of integers to child...
Parent: Sending number 1: 25
Parent: Sending number 2: 30
Parent: Sending number 3: 15
Parent: Sending number 4: 40
Parent: Sending number 5: 10
Child: Reading integers from parent...
Child: Received number 1: 25
Child: Received number 2: 30
Child: Received number 3: 15
Child: Received number 4: 40
Child: Received number 5: 10
Child: Calculated average: 24.00
Child: Sent average to parent
Parent: Received average from child: 24.00
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 %
```

## Task 5: Uppercase Message Transfer between Children

- Parent creates a pipe and two child processes.
- One child writes a string message into the pipe.
- Other child reads the message, converts it to uppercase, and displays it.
- Ensure correct closing of file descriptors.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <ctype.h>

int main() {
    int pipe_fd[2];
    pid_t child1, child2;
    const char message[] = "Hello from child 1! This message will be converted to uppercase.";
    char buffer[100];
    
    if (pipe(pipe_fd) == -1) {
        perror("Pipe creation failed");
        exit(EXIT_FAILURE);
    }
    
    printf("Parent (PID %d): Creating child processes...\n", getpid());
    
    child1 = fork();
    if (child1 < 0) {
        perror("Fork failed for first child");
        exit(EXIT_FAILURE);
    }
    
    if (child1 == 0) {
        close(pipe_fd[0]);
        
        printf("Child 1 (PID %d): Sending message: \"%s\"\n", getpid(), message);
        
        write(pipe_fd[1], message, strlen(message) + 1);
        
        close(pipe_fd[1]);
        
        printf("Child 1 (PID %d): Message sent, exiting\n", getpid());
        exit(EXIT_SUCCESS);
    } 
    else {
        child2 = fork();
        
        if (child2 < 0) {
            perror("Fork failed for second child");
            exit(EXIT_FAILURE);
        }
        
        if (child2 == 0) {
            close(pipe_fd[1]); 
            memset(buffer, 0, sizeof(buffer));
            
            int bytes_read = read(pipe_fd[0], buffer, sizeof(buffer));
            
            if (bytes_read > 0) {
                printf("Child 2 (PID %d): Received message: \"%s\"\n", getpid(), buffer);
                
                for (int i = 0; i < bytes_read; i++) {
                    buffer[i] = toupper(buffer[i]);
                }
                
                printf("Child 2 (PID %d): Message in uppercase: \"%s\"\n", getpid(), buffer);
            } else {
                printf("Child 2 (PID %d): No message received\n", getpid());
            }
            
            close(pipe_fd[0]);
            
            printf("Child 2 (PID %d): Processing complete, exiting\n", getpid());
            exit(EXIT_SUCCESS);
        }
        else {
            close(pipe_fd[0]);
            close(pipe_fd[1]);
            
            printf("Parent (PID %d): Waiting for children to complete\n", getpid());
            
            wait(NULL);
            wait(NULL);
            
            printf("Parent (PID %d): Both children have completed\n", getpid());
        }
    }
    
    return 0;
}
```

### Terminal Output

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 % gcc -o task5 task5.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 % ./task5
Parent (PID 48130): Creating child processes...
Parent (PID 48130): Waiting for children to complete
Child 1 (PID 48135): Sending message: "Hello from child 1! This message will be converted to uppercase."
Child 1 (PID 48135): Message sent, exiting
Child 2 (PID 48136): Received message: "Hello from child 1! This message will be converted to uppercase."
Child 2 (PID 48136): Message in uppercase: "HELLO FROM CHILD 1! THIS MESSAGE WILL BE CONVERTED TO UPPERCASE.
"
Child 2 (PID 48136): Processing complete, exiting
Parent (PID 48130): Both children have completed
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab11 %
```

## Task 6: Named Pipe Producer-Consumer

Create three separate C programs:
- One to create a named pipe using mkfifo().
- A Producer Program that continuously reads strings from user input and writes to the named pipe unless user types "exit".
- A Consumer Program that reads strings using fgets() and displays them.
- Use separate terminals for producer and consumer programs.
- Handle errors and implement synchronization through looped reading/writing.

### Implementation

#### Program 1: Create Named Pipe (create_fifo.c)

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h>
#include <string.h>

#define FIFO_NAME "sp_named_pipe"

int main() {
    printf("Creating named pipe '%s'...\n", FIFO_NAME);
    
    if (mkfifo(FIFO_NAME, 0666) == -1) {
        if (errno != EEXIST) {
            fprintf(stderr, "Error creating named pipe: %s\n", strerror(errno));
            exit(EXIT_FAILURE);
        } else {
            printf("Named pipe already exists. Using existing pipe.\n");
        }
    } else {
        printf("Named pipe created successfully.\n");
    }
    
    printf("Run the producer and consumer in separate terminals.\n");
    printf("- Producer: ./producer\n");
    printf("- Consumer: ./consumer\n");
    
    return 0;
}
```

#### Program 2: Producer (producer.c)

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>

#define FIFO_NAME "sp_named_pipe"
#define MAX_BUFFER 1024

int main() {
    int fd;
    char buffer[MAX_BUFFER];
    
    printf("Producer: Opening FIFO for writing...\n");
    
    fd = open(FIFO_NAME, O_WRONLY);
    if (fd == -1) {
        perror("Producer: Error opening FIFO");
        exit(EXIT_FAILURE);
    }
    
    printf("Producer: FIFO opened successfully.\n");
    printf("Producer: Type messages to send (type 'exit' to quit):\n");
    
    while (1) {
        printf("Producer > ");
        fflush(stdout);
        
        if (fgets(buffer, MAX_BUFFER, stdin) == NULL) {
            break;
        }
        
        size_t len = strlen(buffer);
        if (len > 0 && buffer[len-1] == '\n') {
            buffer[len-1] = '\0'; // Remove newline
            len--;
        }
        
        if (strcmp(buffer, "exit") == 0) {
            printf("Producer: Exiting...\n");
            break;
        }
        
        if (write(fd, buffer, len + 1) == -1) {
            perror("Producer: Error writing to FIFO");
            break;
        }
        
        printf("Producer: Message sent: %s\n", buffer);
    }
    
    close(fd);
    printf("Producer: FIFO closed.\n");
    
    return 0;
}
```

#### Program 3: Consumer (consumer.c)

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>

#define FIFO_NAME "sp_named_pipe"
#define MAX_BUFFER 1024

int main() {
    int fd;
    char buffer[MAX_BUFFER];
    ssize_t bytes_read;
    
    printf("Consumer: Opening FIFO for reading...\n");
    
    fd = open(FIFO_NAME, O_RDONLY);
    if (fd == -1) {
        perror("Consumer: Error opening FIFO");
        exit(EXIT_FAILURE);
    }
    
    printf("Consumer: FIFO opened successfully.\n");
    printf("Consumer: Waiting for messages from producer...\n");
    
    while (1) {
        bytes_read = read(fd, buffer, MAX_BUFFER);
        
        if (bytes_read == -1) {
            perror("Consumer: Error reading from FIFO");
            break;
        }
        
        if (bytes_read == 0) {
            printf("Consumer: FIFO closed by producer. Exiting...\n");
            break;
        }
        
        printf("Consumer: Received message: %s\n", buffer);
    }
    
    close(fd);
    printf("Consumer: FIFO closed.\n");
    
    return 0;
}
```

### Terminal Output

First, create the named pipe:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 % ./create_fifo
Creating named pipe 'sp_named_pipe'...
Named pipe created successfully.
Run the producer and consumer in separate terminals.
- Producer: ./producer
- Consumer: ./consumer
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 %
```

Terminal 1 (Consumer):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 % ./consumer
Consumer: Opening FIFO for reading...
Consumer: FIFO opened successfully.
Consumer: Waiting for messages from producer...
Consumer: Received message: Hello from producer
Consumer: Received message: This is a test message
Consumer: Received message: Testing named pipes
Consumer: FIFO closed by producer. Exiting...
Consumer: FIFO closed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 %
```

Terminal 2 (Producer):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 % ./producer
Producer: Opening FIFO for writing...
Producer: FIFO opened successfully.
Producer: Type messages to send (type 'exit' to quit):
Producer > Hello from producer
Producer: Message sent: Hello from producer
Producer > This is a test message
Producer: Message sent: This is a test message
Producer > Testing named pipes
Producer: Message sent: Testing named pipes
Producer > exit
Producer: Exiting...
Producer: FIFO closed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro task6 %
```

## Conclusion

In this lab, students explored inter-process communication using pipes and popen() in Linux. They implemented both unnamed and named pipes, practiced process forking, and understood data exchange patterns including producer-consumer. These concepts are crucial for understanding low-level process interactions and data sharing in operating systems.
