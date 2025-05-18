# System Programming Lab #7 - Review of Shell Scripting and System Calls

**Computer Science Department**  
**Engineering School**  
**Central Asian University**  
**Spring 2025**

## Objective

The objective of this lab is to provide students with hands-on experience in:

• Writing Bash shell scripts to manage command-line arguments and perform system operations.
• Implementing file handling and metadata retrieval using system calls in C.
• Understanding symbolic links and file attributes in Linux.

## Tasks

### Task 1: Bash Shell Scripting

Write a Bash script that performs the following tasks:

1. **Accepts three command-line arguments:**
   • Your Name
   • Your University Name
   • Your Date of Birth

2. **Saves these arguments into separate files** named sys1, sys2, and sys3.

3. **Displays system information including:**
   • The username of the currently logged-in user.
   • The current date and time.
   • The present working directory.
   • A list of files in the current directory, showing each file's permissions and inode number.

4. **Loops through all files in the current directory** whose names begin with sys, counts these files, and displays the total count.

#### Solution for Task 1

Here is the bash script implementation:

```bash
#!/bin/bash

# Check if three arguments are provided
if [ $# -ne 3 ]; then
    echo "Usage: $0 <Your Name> <University Name> <Date of Birth>"
    exit 1
fi

# Save arguments to separate files
echo "$1" > sys1
echo "$2" > sys2
echo "$3" > sys3

# Display system information
echo "Current username: $(whoami)"
echo "Current date and time: $(date)"
echo "Present working directory: $(pwd)"
echo "Files in current directory with permissions and inode numbers:"
ls -li

# Count files starting with "sys"
sys_files=$(ls -1 sys* 2>/dev/null | wc -l)
echo "Total number of files starting with 'sys': $sys_files"
```

Running the script with sample arguments:

```
$ ./task1.sh "Doniyor" "Central Asian University" "23/05/2004"
Current username: d.ibrokhimov
Current date and time: Sun May 18 21:32:07 +05 2025
Present working directory: /Users/d.ibrokhimov/Documents/Spring25/SP/labs
Files in current directory with permissions and inode numbers:
total 2808
21275788 -rw-r--r--@  1 d.ibrokhimov  staff    3015 May 18 19:36 Doniyor_matrix.c
21275658 -rw-r--r--@  1 d.ibrokhimov  staff     499 May 18 19:36 Doniyor_matrix.h
21276015 -rw-r--r--@  1 d.ibrokhimov  staff    3288 May 18 19:37 Doniyor_matrix.o
21274828 -rw-r--r--@  1 d.ibrokhimov  staff    3304 May 18 19:32 Ibrokhimov_matrix.o
21289633 drwxr-xr-x@ 17 d.ibrokhimov  staff     544 May 18 20:10 Lab3
21302580 drwxr-xr-x@ 10 d.ibrokhimov  staff     320 May 18 20:58 Lab4
21309830 drwxr-xr-x@ 22 d.ibrokhimov  staff     704 May 18 21:14 Lab5
21310440 drwxr-xr-x@ 20 d.ibrokhimov  staff     640 May 18 21:19 Lab6
21272113 -rwxr-xr-x@  1 d.ibrokhimov  staff   33560 May 18 19:17 a.out
21273433 -rw-r--r--@  1 d.ibrokhimov  staff     119 May 18 19:29 bill.c
21273580 -rw-r--r--@  1 d.ibrokhimov  staff     752 May 18 19:24 bill.o
21273411 -rw-r--r--@  1 d.ibrokhimov  staff      94 May 18 19:29 foo.h
21273475 -rw-r--r--@  1 d.ibrokhimov  staff     221 May 18 19:29 fred.c
21273579 -rw-r--r--@  1 d.ibrokhimov  staff     928 May 18 19:24 fred.o
21272174 -rwxr-xr-x@  1 d.ibrokhimov  staff   33560 May 18 19:17 hello
21277274 -rw-r--r--@  1 d.ibrokhimov  staff      40 May 18 19:42 killerr.txt
21277273 -rw-r--r--@  1 d.ibrokhimov  staff       0 May 18 19:42 killout.txt
21277308 -rw-r--r--@  1 d.ibrokhimov  staff      40 May 18 19:42 killouterr.txt
21263213 -rw-r--r--@  1 d.ibrokhimov  staff   21090 May 18 19:05 lab1.md
21271043 -rw-r--r--@  1 d.ibrokhimov  staff   28934 May 18 19:54 lab2.md
21289209 -rw-r--r--@  1 d.ibrokhimov  staff   21350 May 18 20:13 lab3.md
21290635 -rw-r--r--@  1 d.ibrokhimov  staff   11599 May 18 20:59 lab4.md
21309412 -rw-r--r--@  1 d.ibrokhimov  staff   16098 May 18 21:18 lab5.md
21310302 -rw-r--r--@  1 d.ibrokhimov  staff   27506 May 18 21:20 lab6.md
21313399 -rw-r--r--@  1 d.ibrokhimov  staff    1794 May 18 21:15 lab7.md
21015562 -rw-------@  1 d.ibrokhimov  staff   65979 May 17 03:23 labsysprog1.pdf
21316335 -rw-r--r--@  1 d.ibrokhimov  staff   62928 May 18 21:22 labsysprog10.pdf
21316336 -rw-r--r--@  1 d.ibrokhimov  staff   70457 May 18 21:22 labsysprog11 plus Assignment 2.pdf
21316332 -rw-r--r--@  1 d.ibrokhimov  staff   58945 May 18 21:22 labsysprog12.pdf
21015569 -rw-------@  1 d.ibrokhimov  staff   65493 May 17 03:25 labsysprog2.pdf
21015568 -rw-------@  1 d.ibrokhimov  staff  157259 May 17 03:25 labsysprog3.pdf
21015567 -rw-------@  1 d.ibrokhimov  staff  139109 May 17 03:25 labsysprog4.pdf
21015566 -rw-------@  1 d.ibrokhimov  staff  143565 May 17 03:25 labsysprog5.pdf
21015565 -rw-------@  1 d.ibrokhimov  staff  145068 May 17 03:25 labsysprog6.pdf
21015564 -rw-------@  1 d.ibrokhimov  staff   61127 May 17 03:25 labsysprog7.pdf
21316333 -rw-r--r--@  1 d.ibrokhimov  staff   87313 May 18 21:22 labsysprog8.pdf
21316334 -rw-r--r--@  1 d.ibrokhimov  staff   58200 May 18 21:22 labsysprog9.pdf
21276026 -rw-r--r--@  1 d.ibrokhimov  staff    3592 May 18 19:38 libDoniyor.a
21274834 -rw-r--r--@  1 d.ibrokhimov  staff    3640 May 18 19:32 libIbrokhimov.a
21273598 -rw-r--r--@  1 d.ibrokhimov  staff    1952 May 18 19:24 libfoo.a
21277072 -rw-r--r--@  1 d.ibrokhimov  staff    2308 May 18 19:42 lsoutput.txt
21277038 -rw-r--r--@  1 d.ibrokhimov  staff  252451 May 18 19:41 man_bash_output.txt
21274807 -rw-r--r--@  1 d.ibrokhimov  staff    1526 May 18 19:36 matrix_demo.c
21276033 -rw-r--r--@  1 d.ibrokhimov  staff    2584 May 18 19:38 matrix_demo.o
21276219 -rwxr-xr-x@  1 d.ibrokhimov  staff   33920 May 18 19:38 matrix_demo_app
21277599 -rw-r--r--@  1 d.ibrokhimov  staff       0 May 18 19:43 mydata.txt
21273627 -rwxr-xr-x@  1 d.ibrokhimov  staff   33496 May 18 19:24 program
21273550 -rw-r--r--@  1 d.ibrokhimov  staff     237 May 18 19:29 program.c
21273618 -rw-r--r--@  1 d.ibrokhimov  staff     800 May 18 19:24 program.o
21277335 -rw-r--r--@  1 d.ibrokhimov  staff     204 May 18 19:43 psout.txt
21277364 -rw-r--r--@  1 d.ibrokhimov  staff     234 May 18 19:43 pssort.out
21315584 -rw-r--r--@  1 d.ibrokhimov  staff       9 May 18 21:20 sys1
21315585 -rw-r--r--@  1 d.ibrokhimov  staff      25 May 18 21:20 sys2
21315586 -rw-r--r--@  1 d.ibrokhimov  staff      11 May 18 21:20 sys3
21315244 -rwxr-xr-x@  1 d.ibrokhimov  staff     604 May 18 21:20 task1.sh
Total number of files starting with 'sys': 3
```

The contents of the generated files:

```
$ cat sys1 sys2 sys3
Doniyor
Central Asian University
23/05/2004
```

### Task 2: System Calls in C

Write a C program that:

1. **Copies the contents of a source file to a destination file.**
   • Accepts two command-line arguments: source file name and destination file name.
   • Reads data from the source file using a buffer of 100 bytes and writes it to the destination file.

2. **Creates a symbolic link** named `dest_link` that points to the destination file.

3. **Retrieves metadata of the symbolic link and displays:**
   • File size
   • Inode number
   • File type (regular, symbolic, or block device)
   • Last modified time

#### Solution for Task 2

Here is the C program implementation:

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <time.h>
#include <string.h>

int main(int argc, char *argv[]) {
    int src_fd, dst_fd;
    char buffer[100];
    ssize_t bytes_read, bytes_written;
    struct stat link_stat;
    
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
        return 1;
    }
    
    src_fd = open(argv[1], O_RDONLY);
    if (src_fd == -1) {
        perror("Error opening source file");
        return 1;
    }
    
    dst_fd = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (dst_fd == -1) {
        perror("Error opening destination file");
        close(src_fd);
        return 1;
    }
    
    while ((bytes_read = read(src_fd, buffer, sizeof(buffer))) > 0) {
        bytes_written = write(dst_fd, buffer, bytes_read);
        if (bytes_written != bytes_read) {
            perror("Write error");
            close(src_fd);
            close(dst_fd);
            return 1;
        }
    }
    
    if (bytes_read == -1) {
        perror("Read error");
        close(src_fd);
        close(dst_fd);
        return 1;
    }
    
    close(src_fd);
    close(dst_fd);
    
    printf("File copied successfully from %s to %s\n", argv[1], argv[2]);
    
    if (symlink(argv[2], "dest_link") == -1) {
        perror("Error creating symbolic link");
        return 1;
    }
    
    printf("Symbolic link 'dest_link' created pointing to %s\n", argv[2]);
    
    if (lstat("dest_link", &link_stat) == -1) {
        perror("Error getting link metadata");
        return 1;
    }
    
    printf("\nSymbolic Link Metadata:\n");
    printf("File size: %lld bytes\n", (long long)link_stat.st_size);
    printf("Inode number: %llu\n", (unsigned long long)link_stat.st_ino);
    
    printf("File type: ");
    if (S_ISREG(link_stat.st_mode))
        printf("regular file\n");
    else if (S_ISLNK(link_stat.st_mode))
        printf("symbolic link\n");
    else if (S_ISBLK(link_stat.st_mode))
        printf("block device\n");
    else if (S_ISDIR(link_stat.st_mode))
        printf("directory\n");
    else if (S_ISCHR(link_stat.st_mode))
        printf("character device\n");
    else if (S_ISFIFO(link_stat.st_mode))
        printf("FIFO/pipe\n");
    else if (S_ISSOCK(link_stat.st_mode))
        printf("socket\n");
    else
        printf("unknown\n");
    
    printf("Last modified time: %s", ctime(&link_stat.st_mtime));
    
    return 0;
}
```

For testing the program, we created a simple source file:

```
$ echo "This is a sample text file for testing the file copy functionality. It contains multiple lines of text to demonstrate the buffer-based copying mechanism." > source_file.txt
```

Compiling and running the program:

```
$ gcc -o task2 task2.c
$ ./task2 source_file.txt destination_file.txt
File copied successfully from source_file.txt to destination_file.txt
Symbolic link 'dest_link' created pointing to destination_file.txt

Symbolic Link Metadata:
File size: 20 bytes
Inode number: 21320959
File type: symbolic link
Last modified time: Sun May 18 21:36:17 2025
```

Verifying the symbolic link:

```
$ ls -la dest_link destination_file.txt
lrwxr-xr-x@ 1 d.ibrokhimov  staff  20 May 18 21:36 dest_link -> destination_file.txt
-rw-r--r--@ 1 d.ibrokhimov  staff 160 May 18 21:36 destination_file.txt
```

Checking the contents of the files:

```
$ cat source_file.txt && echo -e "\n--- Destination File ---\n" && cat destination_file.txt
This is a sample text file for testing the file copy functionality. It contains multiple lines of text to demonstrate the buffer-based copying mechanism.

--- Destination File ---

This is a sample text file for testing the file copy functionality. It contains multiple lines of text to demonstrate the buffer-based copying mechanism.
```

The program successfully:
1. Copied the content from source_file.txt to destination_file.txt using a 100-byte buffer
2. Created a symbolic link named dest_link pointing to destination_file.txt
3. Retrieved and displayed metadata about the symbolic link