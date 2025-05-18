# LAB 5 - File Handling in Linux Using System Calls

**Department of Computer Science, School of Engineering**  
**Report Submission Date:** Due in Next Lab

## Objectives

The objective of this lab is to develop proficiency in file handling in Linux using system calls such as open, read, and write. Students will explore opening files, reading and writing data, and copying file contents efficiently, while implementing robust error handling using perror for detailed diagnostics.

## Instructions

• Utilize the lab time effectively; avoid distractions such as cell phones.
• Use personal systems running Ubuntu for flexibility; macOS users should adapt commands accordingly.
• Create a Lab5 directory to store all programs. Compile and test each program individually.
• Test programs with sample files (e.g., create sample.txt using echo "Test data" > sample.txt).
• Refrain from copying solutions directly; understand the system calls and experiment with variations.
• Ensure proper file descriptor closure to prevent resource leaks.

## Tasks

### Task 1: Opening a File in Read-Only Mode

Write a C program that uses the open system call to open a file named sample.txt in read-only mode. Ensure that the program prints an appropriate error message if the file doesn't exist or if there's an error opening the file. Use the perror function instead of printf, and note the advantage of using it in your observations.

#### Solution:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("sample.txt", O_RDONLY);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }
    
    printf("File opened successfully\n");
    close(fd);
    return 0;
}
```

#### Output (when file exists):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task1 task1.c && ./task1
File opened successfully
```

#### Output (when file does not exist):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task1_nonexistent task1_nonexistent.c && ./task1_nonexistent
Error opening file: No such file or directory
```

#### Observations:
The advantage of using perror() over printf() is that perror automatically includes the system error message that corresponds to the errno value. This provides more detailed diagnostic information about what went wrong without additional coding effort. In the example above, perror() added "No such file or directory" which precisely describes the nature of the error.

### Task 2: Reading File Contents

Extend the program from Task 1 to include the read system call. After successfully opening sample.txt, read its contents into a buffer of size 1024 bytes using the read system call. Print the contents of the file to standard output. Ensure error handling is included for the reading operation.

#### Solution:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("sample.txt", O_RDONLY);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }
    
    char buffer[1024];
    ssize_t bytes_read = read(fd, buffer, sizeof(buffer) - 1);
    
    if (bytes_read == -1) {
        perror("Error reading file");
        close(fd);
        return 1;
    }
    
    buffer[bytes_read] = '\0';
    printf("File contents:\n%s\n", buffer);
    
    close(fd);
    return 0;
}
```

#### Output (when file exists):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task2 task2.c && ./task2
File contents:
This is a test file.
It contains multiple lines.
Use this for testing the file handling operations.
```

#### Output (when file does not exist):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task2_nonexistent task2_nonexistent.c && ./task2_nonexistent
Error opening file: No such file or directory
```

#### Observations:
The read system call returns the number of bytes that were successfully read, which we store in bytes_read. This value is used to null-terminate the buffer by placing '\0' at the position immediately after the last byte read. This ensures that the buffer is correctly formatted as a C string for printing with printf. The error handling remains consistent, using perror() to provide detailed error information if the read operation fails.

### Task 3: Creating and Writing to a File

Write a C program that creates a new file named output.txt using the open system call in write-only mode. Then, using the write system call, write the following text to the file: "Hello, this is a test message." Ensure proper error handling for file creation and writing operations.

#### Solution:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    const char *message = "Hello, this is a test message.";
    int fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }
    
    ssize_t bytes_written = write(fd, message, strlen(message));
    
    if (bytes_written == -1) {
        perror("Error writing to file");
        close(fd);
        return 1;
    }
    
    if (bytes_written != strlen(message)) {
        fprintf(stderr, "Warning: Not all bytes were written!\n");
    }
    
    printf("Successfully wrote %zd bytes to output.txt\n", bytes_written);
    
    if (close(fd) == -1) {
        perror("Error closing file");
        return 1;
    }
    
    return 0;
}
```

#### Output (successful execution):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task3 task3.c && ./task3
Successfully wrote 30 bytes to output.txt
```

#### Verifying file contents:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % cat output.txt
Hello, this is a test message.
```

#### Observations:
The program opens a file in write-only mode (O_WRONLY) with flags for creating the file if it doesn't exist (O_CREAT) and truncating it if it does (O_TRUNC). The 0644 permission parameter gives read and write permission to the owner and read permission to group and others. The write system call returns the number of bytes written, which we check against the expected message length. We also handle potential file closing errors.

### Task 4: Copying File Contents with Command-Line Arguments

Write a C program that takes two command-line arguments: the names of an input file and an output file. Use the open, read, and write system calls to copy the contents of the input file to the output file. Your program should handle the following scenarios:

• Verify that the correct number of command-line arguments are provided. If not, print a usage message indicating the correct usage of the program.
• If the input file cannot be opened for reading, print an error message and exit the program with a non-zero exit status.
• If the output file cannot be opened for writing, print an error message and exit the program with a non-zero exit status.
• Ensure that the program can handle copying large files efficiently by reading and writing data in chunks rather than all at once. Properly close any opened files and handle any errors that may occur during the file copying process.

#### Solution:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

#define BUFFER_SIZE 4096

int main(int argc, char *argv[]) {
    int fd_src, fd_dest;
    ssize_t bytes_read, bytes_written;
    char buffer[BUFFER_SIZE];
    
    if (argc != 3) {
        fprintf(stderr, "Usage: %s source_file destination_file\n", argv[0]);
        return 1;
    }
    
    fd_src = open(argv[1], O_RDONLY);
    if (fd_src == -1) {
        perror("Error opening source file");
        return 1;
    }
    
    fd_dest = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd_dest == -1) {
        perror("Error opening destination file");
        close(fd_src);
        return 1;
    }
    
    while ((bytes_read = read(fd_src, buffer, BUFFER_SIZE)) > 0) {
        bytes_written = write(fd_dest, buffer, bytes_read);
        
        if (bytes_written == -1) {
            perror("Error writing to destination file");
            close(fd_src);
            close(fd_dest);
            return 1;
        }
        
        if (bytes_written != bytes_read) {
            fprintf(stderr, "Warning: Only %zd bytes out of %zd bytes were written\n",
                    bytes_written, bytes_read);
        }
    }
    
    if (bytes_read == -1) {
        perror("Error reading from source file");
        close(fd_src);
        close(fd_dest);
        return 1;
    }
    
    printf("File copy completed successfully\n");
    
    if (close(fd_src) == -1) {
        perror("Error closing source file");
        close(fd_dest);
        return 1;
    }
    
    if (close(fd_dest) == -1) {
        perror("Error closing destination file");
        return 1;
    }
    
    return 0;
}
```

#### Output (successful execution):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % gcc -o task4 task4.c
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % ./task4 large_test.txt copied_file.txt
File copy completed successfully
```

#### Verifying the copied file contents:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % cat copied_file.txt
Line 1: This is a larger test file for Task 4.
Line 2: We're testing file copying functionality.
Line 3: This file will be used to test the buffer-based copying.
Line 4: The program should handle files of any size efficiently.
Line 5: Using system calls directly provides more control over I/O operations.
```

#### Output (incorrect arguments):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % ./task4
Usage: ./task4 source_file destination_file
```

#### Output (non-existent source file):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab5 % ./task4 nonexistent.txt output.txt
Error opening source file: No such file or directory
```

#### Observations:
This program efficiently handles file copying by using a buffer of fixed size (4096 bytes) and reading/writing in chunks, making it suitable for large files. The program processes command-line arguments to get source and destination file paths and includes robust error handling for all file operations. It verifies that the correct number of arguments is provided and properly closes file descriptors in all error conditions to prevent resource leaks.

## System Calls Reference

This section provides details on the system calls used in this lab: open, read, write, and close.

### open

**Prototype:** `int open(const char *pathname, int flags, mode_t mode);`

• **Description:** Opens a file and returns a file descriptor. Used to access files for reading, writing, or both.
• **Parameters:**
  - pathname: Path to the file (e.g., "sample.txt").
  - flags: Options like O_RDONLY (read-only), O_WRONLY (write-only), O_CREAT (create if not exists).
  - mode: Permissions (e.g., 0644 for rw-r--r--) if O_CREAT is used.
• **Return Value:** File descriptor on success, -1 on error (sets errno).

### read

**Prototype:** `ssize_t read(int fd, void *buf, size_t count);`

• **Description:** Reads data from a file descriptor into a buffer.
• **Parameters:**
  - fd: File descriptor from open.
  - buf: Buffer to store read data.
  - count: Maximum bytes to read.
• **Return Value:** Number of bytes read, 0 at end of file, -1 on error.

### write

**Prototype:** `ssize_t write(int fd, const void *buf, size_t count);`

• **Description:** Writes data from a buffer to a file descriptor.
• **Parameters:**
  - fd: File descriptor from open.
  - buf: Buffer containing data to write.
  - count: Number of bytes to write.
• **Return Value:** Number of bytes written, -1 on error.

### close

**Prototype:** `int close(int fd);`

• **Description:** Closes a file descriptor, freeing system resources.
• **Parameters:**
  - fd: File descriptor to close.
• **Return Value:** 0 on success, -1 on error.

## Example Commands

1. **Listing Files with Inodes**
   ```bash
   ls -li
   ```
   Displays files with inode numbers, useful for identifying hard and soft links.

2. **Creating a Symbolic Link**
   ```bash
   ln -s xyx softlink
   ```
   Creates a symbolic (soft) link named softlink pointing to xyx.

3. **Creating a Hard Link**
   ```bash
   ln abc hardlink
   ```
   Creates a hard link named hardlink for the file abc.

4. **Listing Device Files**
   ```bash
   ls /dev | less
   ```
   Lists device files in /dev, piped to less for scrolling.

5. **Mounting a Flash Device**
   ```bash
   sudo mount /dev/sdb1 /mnt
   ```
   Mounts a flash device (e.g., /dev/sdb1) to /mnt. Replace sdb1 with the actual device name (use lsblk to identify).

6. **Listing Block Devices**
   ```bash
   lsblk
   ```
   Lists block devices (e.g., disks, USB drives) and their partitions, useful for identifying device names like sdb1 for mounting.

7. **Listing Files with Detailed Information**
   ```bash
   ls -ls
   ```
   Lists files with their sizes in blocks and detailed information.

8. **Recording Terminal Session**
   ```bash
   script file.log
   ```
   Starts recording the terminal session into file.log.

9. **Stopping the Script Logging**
   ```bash
   exit
   ```
   Stops recording and saves the session into file.log.

10. **Using tee to Save and Append Command Output**
    ```bash
    ls -l | tee -a output.txt
    ```
    Displays the output of ls -l and appends it to output.txt without overwriting existing content.

11. **Setting Default File Permissions**
    ```bash
    umask 027
    ```
    Sets the default permissions for new files and directories (removes group write and others' read/write permissions).

12. **Changing File Ownership**
    ```bash
    sudo chown user:group filename
    ```
    Changes ownership of filename to the specified user and group. Requires sudo for system-wide changes.

13. **Replacing Consecutive Dashes with a Single Dash**
    ```bash
    echo "hello---world---test" | tr -s "-"
    ```
    Compresses multiple consecutive dashes into a single dash, outputting hello-world-test.

## Practical Example

Monitoring system resource usage is crucial for performance analysis and troubleshooting. This C program logs CPU and memory usage by reading data from the /proc filesystem and appending it to a log file. The logging interval is set to 5 seconds, making it suitable for real-time monitoring applications.

### C Code for System Logging

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <time.h>

#define BUFFER_SIZE 256
#define LOG_INTERVAL 5 // Log every 5 seconds

void log_system_usage() {
    int cpu_fd, mem_fd, log_fd;
    char buffer[BUFFER_SIZE];
    time_t now;
    char timestamp[64];

    log_fd = open("system_log.txt", O_WRONLY | O_CREAT | O_APPEND, 0644);
    if (log_fd < 0) {
        perror("Error opening log file");
        return;
    }

    time(&now);
    strftime(timestamp, sizeof(timestamp), "%Y-%m-%d %H:%M:%S", localtime(&now));
    write(log_fd, "\n===== System Usage Log - ", 26);
    write(log_fd, timestamp, strlen(timestamp));
    write(log_fd, " =====\n", 7);

    cpu_fd = open("/proc/stat", O_RDONLY);
    if (cpu_fd < 0) {
        perror("Error opening /proc/stat");
        close(log_fd);
        return;
    }

    mem_fd = open("/proc/meminfo", O_RDONLY);
    if (mem_fd < 0) {
        perror("Error opening /proc/meminfo");
        close(cpu_fd);
        close(log_fd);
        return;
    }

    ssize_t bytes_read = read(cpu_fd, buffer, BUFFER_SIZE - 1);
    if (bytes_read > 0) {
        buffer[bytes_read] = '\0';
        write(log_fd, "CPU Usage:\n", 11);
        write(log_fd, buffer, bytes_read);
        write(log_fd, "\n", 1);
    }

    bytes_read = read(mem_fd, buffer, BUFFER_SIZE - 1);
    if (bytes_read > 0) {
        buffer[bytes_read] = '\0';
        write(log_fd, "Memory Usage:\n", 14);
        write(log_fd, buffer, bytes_read);
        write(log_fd, "\n", 1);
    }

    close(cpu_fd);
    close(mem_fd);
    close(log_fd);

    printf("System usage logged successfully in system_log.txt\n");
}

int main() {
    while (1) {
        log_system_usage();
        sleep(LOG_INTERVAL);
    }
    return 0;
}
```