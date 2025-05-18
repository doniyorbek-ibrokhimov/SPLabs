# LAB 6 - File Handling in Linux Using lseek, fstat, stat, lstat, dup, dup2, and stdio Functions

**Department of Computer Science, School of Engineering**  
**Report Submission Date:** Due in Next Lab

## Objectives

The objective of this lab is to gain proficiency in advanced file handling in Linux using system calls such as lseek, fstat, stat, lstat, dup, and dup2, alongside standard I/O functions like fopen, fprintf, sprintf, and sscanf. Students will learn to manipulate file offsets, retrieve file metadata, duplicate file descriptors, and format and parse data from files, while ensuring robust error handling with perror.

## Instructions

• Use lab time efficiently; avoid distractions such as cell phones.
• Work on personal systems running Ubuntu; macOS users should adapt commands as needed.
• Create a Lab6 directory to store all programs. Compile and test each program individually.
• Test programs with sample files (e.g., create sample.txt using echo "Test data" > sample.txt).
• Avoid copying solutions; experiment with system calls and stdio functions to deepen understanding.
• Close file descriptors properly to prevent resource leaks.

## Tasks

### Task 1: Using lseek to Move File Offset

Write a C program that opens a file named sample.txt in read-only mode using open. Use lseek to move the file offset to the 5th byte and read the next 10 bytes into a buffer. Print the contents and handle errors using perror. Observe how lseek affects the file offset.

**Solution:**

First, we create the `Lab6` directory and our `sample.txt` file:
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % mkdir -p SP/labs/Lab6
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % echo "This is a test file with some data for lseek and stat." > SP/labs/Lab6/sample.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cat SP/labs/Lab6/sample.txt
This is a test file with some data for lseek and stat.
```

**`SP/labs/Lab6/task1.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>

int main() {
    int fd;
    char buffer[11];
    ssize_t bytes_read;

    fd = open("SP/labs/Lab6/sample.txt", O_RDONLY);
    if (fd == -1) {
        perror("Error opening file");
        return EXIT_FAILURE;
    }

    if (lseek(fd, 5, SEEK_SET) == -1) {
        perror("Error seeking in file");
        close(fd);
        return EXIT_FAILURE;
    }

    bytes_read = read(fd, buffer, 10);
    if (bytes_read == -1) {
        perror("Error reading file");
        close(fd);
        return EXIT_FAILURE;
    }
    buffer[bytes_read] = '\\0';

    printf("Read from file: %s\\n", buffer);

    if (close(fd) == -1) {
        perror("Error closing file");
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

**Compilation and Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task1.c -o SP/labs/Lab6/task1_program && SP/labs/Lab6/task1_program
Read from file: is a test 
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Observation:**
The program opens `SP/labs/Lab6/sample.txt`. The content of this file is "This is a test file with some data for lseek and stat.".
`lseek(fd, 5, SEEK_SET)` moves the file offset to the 5th byte (0-indexed), which is the character 'i' in "is a test".
`read(fd, buffer, 10)` reads the next 10 bytes: "is a test ".
The program then prints "Read from file: is a test ".

### Task 2: Retrieving File Metadata with stat and lstat

Write a C program that uses stat and lstat to retrieve metadata for sample.txt and a symbolic link to it (create one using ln -s sample.txt samplelink). Print the file size, inode number, and whether it\'s a regular file or symbolic link. Compare the results of stat and lstat and note differences in your observations.

**Solution:**

First, we create the symbolic link `samplelink` pointing to `sample.txt`. We assume this is done from within the `SP/labs/Lab6` directory.
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab6
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab6 % pwd
/Users/d.ibrokhimov/Documents/Spring25/SP/labs/Lab6
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab6 % ln -s sample.txt samplelink
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab6 % ls -l sample.txt samplelink
lrwxr-xr-x  1 d.ibrokhimov  staff   10 May 18 21:07 samplelink -> sample.txt
-rw-r--r--  1 d.ibrokhimov  staff   55 May 18 21:05 sample.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab6 % cd ../../.. 
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % 
```

**`SP/labs/Lab6/task2.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <unistd.h>
#include <time.h>

void print_stat_info(const char *filename, struct stat *sb) {
    printf("Metadata for: %s\\n", filename);
    printf("  File size: %lld bytes\\n", (long long) sb->st_size);
    printf("  Inode number: %llu\\n", (unsigned long long) sb->st_ino);
    printf("  File type: ");
    if (S_ISREG(sb->st_mode)) {
        printf("Regular file\\n");
    } else if (S_ISLNK(sb->st_mode)) {
        printf("Symbolic link\\n");
    } else if (S_ISDIR(sb->st_mode)) {
        printf("Directory\\n");
    } else {
        printf("Other\\n");
    }
    printf("\\n");
}

int main() {
    struct stat stat_buf;
    const char* file_path = "SP/labs/Lab6/sample.txt";
    const char* link_path = "SP/labs/Lab6/samplelink";

    printf("Using stat (follows symbolic links):\\n");
    if (stat(file_path, &stat_buf) == -1) {
        perror("stat for sample.txt failed");
    } else {
        print_stat_info(file_path, &stat_buf);
    }

    if (stat(link_path, &stat_buf) == -1) {
        perror("stat for samplelink failed");
    } else {
        print_stat_info(link_path, &stat_buf);
    }

    printf("\\nUsing lstat (does not follow symbolic links):\\n");
    if (lstat(file_path, &stat_buf) == -1) {
        perror("lstat for sample.txt failed");
    } else {
        print_stat_info(file_path, &stat_buf);
    }

    if (lstat(link_path, &stat_buf) == -1) {
        perror("lstat for samplelink failed");
    } else {
        print_stat_info(link_path, &stat_buf);
    }

    return EXIT_SUCCESS;
}
```

**Compilation and Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task2.c -o SP/labs/Lab6/task2_program && SP/labs/Lab6/task2_program
Using stat (follows symbolic links):
Metadata for: SP/labs/Lab6/sample.txt
  File size: 55 bytes
  Inode number: 21310441
  File type: Regular file

Metadata for: SP/labs/Lab6/samplelink
  File size: 55 bytes
  Inode number: 21310441
  File type: Regular file


Using lstat (does not follow symbolic links):
Metadata for: SP/labs/Lab6/sample.txt
  File size: 55 bytes
  Inode number: 21310441
  File type: Regular file

Metadata for: SP/labs/Lab6/samplelink
  File size: 10 bytes
  Inode number: 21310725
  File type: Symbolic link

d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Observations:**
- **`stat` on `sample.txt`**: Reports it as a regular file with its actual size (55 bytes) and inode number.
- **`stat` on `samplelink`**: Since `stat` follows symbolic links, it reports the metadata of the target file (`sample.txt`). Thus, it shows `samplelink` as a regular file with the same size (55 bytes) and inode number as `sample.txt`.
- **`lstat` on `sample.txt`**: Behaves the same as `stat` for a regular file, reporting its actual size and inode.
- **`lstat` on `samplelink`**: Since `lstat` does *not* follow symbolic links, it reports metadata about the link itself. It correctly identifies `samplelink` as a symbolic link. The size reported (10 bytes) is the length of the pathname stored in the symbolic link (i.e., the length of "sample.txt"). The inode number is different from `sample.txt` because the link is a separate entry in the file system.

This demonstrates the key difference: `stat` provides information about the file a symbolic link points to, while `lstat` provides information about the symbolic link itself.

### Task 3: Duplicating File Descriptors with dup and dup2

Write a C program that opens sample.txt in read-only mode and duplicates its file descriptor using dup using this new descriptor read the file contents. Then, open a new file output1.txt in write-only mode and use dup2 to redirect standard output (file descriptor 1) to output1.txt. Print a message using printf and verify it's written to output.txt. Include error handling.

**Solution:**

**`SP/labs/Lab6/task3.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <errno.h>

int main() {
    int fd_sample, fd_dup, fd_output;
    char buffer[100];
    ssize_t bytes_read;

    fd_sample = open("SP/labs/Lab6/sample.txt", O_RDONLY);
    if (fd_sample == -1) {
        perror("Error opening sample.txt");
        return EXIT_FAILURE;
    }

    fd_dup = dup(fd_sample);
    if (fd_dup == -1) {
        perror("Error duplicating file descriptor");
        close(fd_sample);
        return EXIT_FAILURE;
    }

    bytes_read = read(fd_dup, buffer, sizeof(buffer) - 1);
    if (bytes_read == -1) {
        perror("Error reading from file");
        close(fd_sample);
        close(fd_dup);
        return EXIT_FAILURE;
    }
    buffer[bytes_read] = '\\0';

    printf("Reading from duplicated descriptor (fd=%d):\\n%s\\n\\n", fd_dup, buffer);

    fd_output = open("SP/labs/Lab6/output1.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd_output == -1) {
        perror("Error opening output1.txt");
        close(fd_sample);
        close(fd_dup);
        return EXIT_FAILURE;
    }

    printf("Before redirection: This message goes to terminal\\n");

    if (dup2(fd_output, STDOUT_FILENO) == -1) {
        perror("Error redirecting standard output");
        close(fd_sample);
        close(fd_dup);
        close(fd_output);
        return EXIT_FAILURE;
    }

    printf("After redirection: This message goes to output1.txt\\n");

    if (close(fd_sample) == -1) {
        perror("Error closing sample file descriptor");
        return EXIT_FAILURE;
    }
    if (close(fd_dup) == -1) {
        perror("Error closing duplicated file descriptor");
        return EXIT_FAILURE;
    }
    if (close(fd_output) == -1) {
        perror("Error closing output file descriptor");
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

**Compilation and Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task3.c -o SP/labs/Lab6/task3_program && SP/labs/Lab6/task3_program
Reading from duplicated descriptor (fd=4):
This is a test file with some data for lseek and stat.

Before redirection: This message goes to terminal
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Verification of content in output1.txt:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cat SP/labs/Lab6/output1.txt
After redirection: This message goes to output1.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Observations:**
- The program successfully opens the `sample.txt` file using `open()` and gets a file descriptor.
- Using `dup()`, we create a duplicate file descriptor pointing to the same file, which has a new descriptor number (4 in this execution).
- The duplicate descriptor can be used to read the file's contents, demonstrating that it works exactly like the original descriptor.
- Next, a new file `output1.txt` is created for writing.
- The program prints a message to the terminal before redirection.
- Using `dup2()`, we redirect standard output (file descriptor 1) to `output1.txt`.
- After redirection, any printf output goes to the file instead of the terminal.
- The program successfully demonstrates file descriptor duplication with both `dup()` and `dup2()`.

### Task 4: Formatting File Metadata with sprintf

Write a C program that uses stat to retrieve metadata for sample.txt. Use sprintf to format a string containing the file's size (in bytes), last modification time raw, and inode number. Write this formatted string to a file named metadata.txt using fopen and fprintf. Include error handling for file operations and note the advantage of using sprintf over direct string concatenation in your observations.

**Solution:**

**`SP/labs/Lab6/task4.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <unistd.h>
#include <time.h>
#include <errno.h>
#include <string.h>

int main() {
    struct stat file_stat;
    char metadata_str[256];
    char time_str[64];
    const char* file_path = "SP/labs/Lab6/sample.txt";
    
    if (stat(file_path, &file_stat) == -1) {
        perror("Error getting file stats");
        return EXIT_FAILURE;
    }
    
    time_t mod_time = file_stat.st_mtime;
    struct tm *timeinfo = localtime(&mod_time);
    if (timeinfo == NULL) {
        perror("Error converting time");
        return EXIT_FAILURE;
    }
    
    if (strftime(time_str, sizeof(time_str), "%Y-%m-%d %H:%M:%S", timeinfo) == 0) {
        fprintf(stderr, "Error formatting time string\\n");
        return EXIT_FAILURE;
    }
    
    int result = sprintf(metadata_str, 
                        "File: %s\\n"
                        "Size: %lld bytes\\n"
                        "Inode: %llu\\n"
                        "Last modification time (raw): %ld\\n"
                        "Last modification time: %s\\n",
                        file_path,
                        (long long)file_stat.st_size,
                        (unsigned long long)file_stat.st_ino,
                        (long)mod_time,
                        time_str);
    
    if (result < 0) {
        fprintf(stderr, "Error formatting metadata string\\n");
        return EXIT_FAILURE;
    }
    
    printf("Formatted metadata using sprintf:\\n%s\\n", metadata_str);
    
    FILE *metadata_file = fopen("SP/labs/Lab6/metadata.txt", "w");
    if (metadata_file == NULL) {
        perror("Error opening metadata.txt");
        return EXIT_FAILURE;
    }
    
    if (fprintf(metadata_file, "%s", metadata_str) < 0) {
        perror("Error writing to metadata.txt");
        fclose(metadata_file);
        return EXIT_FAILURE;
    }
    
    fprintf(metadata_file, "\\nAdvantage of sprintf: It allows formatting of various data types into a \
single string buffer before writing, making it more efficient than multiple \
separate write operations. It also provides type-safety and consistent formatting \
controls (width, precision, alignment) compared to manual string concatenation.\\n");
    
    printf("Metadata has been written to metadata.txt\\n");
    
    if (fclose(metadata_file) != 0) {
        perror("Error closing metadata.txt");
        return EXIT_FAILURE;
    }
    
    return EXIT_SUCCESS;
}
```

**Compilation and Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task4.c -o SP/labs/Lab6/task4_program && SP/labs/Lab6/task4_program
Formatted metadata using sprintf:
File: SP/labs/Lab6/sample.txt
Size: 55 bytes
Inode: 21310441
Last modification time (raw): 1747584321
Last modification time: 2025-05-18 21:05:21

Metadata has been written to metadata.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Contents of metadata.txt:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cat SP/labs/Lab6/metadata.txt
File: SP/labs/Lab6/sample.txt
Size: 55 bytes
Inode: 21310441
Last modification time (raw): 1747584321
Last modification time: 2025-05-18 21:05:21

Advantage of sprintf: It allows formatting of various data types into a single string buffer before writing, making it more efficient than multiple separate write operations. It also provides type-safety and consistent formatting controls (width, precision, alignment) compared to manual string concatenation.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Observations:**
- The program successfully retrieves file metadata using `stat()`.
- It converts the Unix timestamp to a human-readable date/time format using `localtime()` and `strftime()`.
- Using `sprintf()`, all metadata is formatted into a single string buffer with proper formatting.
- The formatted metadata is both displayed on-screen and written to `metadata.txt` using `fprintf()`.
- The advantage of using `sprintf()` over direct string concatenation is that it provides a clean, type-safe way to format different data types (integers, strings, etc.) into a single string with consistent formatting.
- Error checking is implemented at each step to ensure robust operation.

### Task 5: Parsing File Data with sscanf

Write a C program that reads a line from a file named data.txt (create it with content like "Item: 42 3.14 Widget" using echo). Use fopen and fgets to read the line into a buffer, then use sscanf to parse the line into an integer (e.g., 42), a float (e.g., 3.14), and a string (e.g., "Widget"). Print the parsed values to standard output and handle errors if the file doesn't exist or the parsing fails.

**Solution:**

First, we create the `data.txt` file with formatted data for parsing:
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % echo "Item: 42 3.14 Widget" > SP/labs/Lab6/data.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cat SP/labs/Lab6/data.txt
Item: 42 3.14 Widget
```

**`SP/labs/Lab6/task5.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    FILE *file;
    char buffer[100];
    int item_number;
    float item_value;
    char item_name[50];
    
    file = fopen("SP/labs/Lab6/data.txt", "r");
    if (file == NULL) {
        perror("Error opening data.txt");
        return EXIT_FAILURE;
    }
    
    if (fgets(buffer, sizeof(buffer), file) == NULL) {
        perror("Error reading from data.txt");
        fclose(file);
        return EXIT_FAILURE;
    }
    
    printf("Read from file: %s", buffer);
    
    int result = sscanf(buffer, "Item: %d %f %s", &item_number, &item_value, item_name);
    
    if (result != 3) {
        fprintf(stderr, "Error parsing data: expected 3 values, got %d\\n", result);
        fclose(file);
        return EXIT_FAILURE;
    }
    
    printf("\\nParsed values using sscanf:\\n");
    printf("  Item number: %d\\n", item_number);
    printf("  Item value: %.2f\\n", item_value);
    printf("  Item name: %s\\n", item_name);
    
    fclose(file);
    return EXIT_SUCCESS;
}
```

**Compilation and Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task5.c -o SP/labs/Lab6/task5_program && SP/labs/Lab6/task5_program
Read from file: Item: 42 3.14 Widget

Parsed values using sscanf:
  Item number: 42
  Item value: 3.14
  Item name: Widget
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Error Handling Test:**

To demonstrate error handling with improperly formatted data, we created an additional file and a modified version of the program:

```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % echo "Incorrect format data" > SP/labs/Lab6/bad_data.txt
```

**`SP/labs/Lab6/task5_error.c`**
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int parse_file(const char *filename) {
    FILE *file;
    char buffer[100];
    int item_number;
    float item_value;
    char item_name[50];
    
    file = fopen(filename, "r");
    if (file == NULL) {
        perror("Error opening file");
        return EXIT_FAILURE;
    }
    
    if (fgets(buffer, sizeof(buffer), file) == NULL) {
        perror("Error reading from file");
        fclose(file);
        return EXIT_FAILURE;
    }
    
    printf("Read from %s: %s", filename, buffer);
    
    int result = sscanf(buffer, "Item: %d %f %s", &item_number, &item_value, item_name);
    
    if (result != 3) {
        fprintf(stderr, "Error parsing data: expected 3 values, got %d\\n", result);
        fclose(file);
        return EXIT_FAILURE;
    }
    
    printf("\\nParsed values using sscanf:\\n");
    printf("  Item number: %d\\n", item_number);
    printf("  Item value: %.2f\\n", item_value);
    printf("  Item name: %s\\n", item_name);
    
    fclose(file);
    return EXIT_SUCCESS;
}

int main() {
    printf("\\n=== Processing valid data ===\\n");
    int result1 = parse_file("SP/labs/Lab6/data.txt");
    
    printf("\\n=== Processing invalid data ===\\n");
    int result2 = parse_file("SP/labs/Lab6/bad_data.txt");
    
    return (result1 == EXIT_SUCCESS) ? EXIT_SUCCESS : EXIT_FAILURE;
}
```

**Error Handling Test Execution:**
```bash
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % gcc SP/labs/Lab6/task5_error.c -o SP/labs/Lab6/task5_error_program && SP/labs/Lab6/task5_error_program

=== Processing valid data ===
Read from SP/labs/Lab6/data.txt: Item: 42 3.14 Widget

Parsed values using sscanf:
  Item number: 42
  Item value: 3.14
  Item name: Widget

=== Processing invalid data ===
Read from SP/labs/Lab6/bad_data.txt: Incorrect format data
Error parsing data: expected 3 values, got 0
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

**Observations:**
- The program successfully opens `data.txt` using `fopen()` and reads its content with `fgets()`.
- The formatted data follows the pattern "Item: [number] [float] [string]".
- `sscanf()` is used to parse the buffer into three different variables with their appropriate data types:
  - An integer (`item_number = 42`) 
  - A floating-point number (`item_value = 3.14`)
  - A string (`item_name = "Widget"`)
- The program demonstrates proper error handling for file operations and parsing operations.
- When processing improperly formatted data (as shown in the error handling test), the `sscanf()` function returns 0, indicating that no items were successfully parsed according to the format string. The program correctly detects this error, displays an appropriate error message, and exits safely.
- The result of `sscanf()` returns the number of items successfully parsed, which is checked to ensure all expected values were extracted.

## System Calls Reference

This section provides details on the system calls used in this lab: lseek, fstat, stat, lstat, dup, and dup2.

### lseek

**Prototype:** `off_t lseek(int fd, off_t offset, int whence);`

• **Description:** Repositions the file offset of the file descriptor.
• **Parameters:**
  - fd: File descriptor.
  - offset: Number of bytes to move.
  - whence: SEEK_SET (absolute), SEEK_CUR (relative to current), SEEK_END (relative to end).
• **Return Value:** New offset on success, -1 on error.

### fstat

**Prototype:** `int fstat(int fd, struct stat *buf);`

• **Description:** Retrieves file metadata using a file descriptor.
• **Parameters:**
  - fd: File descriptor.
  - buf: Pointer to struct stat for storing metadata.
• **Return Value:** 0 on success, -1 on error.

### stat

**Prototype:** `int stat(const char *pathname, struct stat *buf);`

• **Description:** Retrieves file metadata by pathname, following symbolic links.
• **Parameters:**
  - pathname: Path to the file.
  - buf: Pointer to struct stat.
• **Return Value:** 0 on success, -1 on error.

### lstat

**Prototype:** `int lstat(const char *pathname, struct stat *buf);`

• **Description:** Similar to stat, but does not follow symbolic links.
• **Parameters:** Same as stat.
• **Return Value:** 0 on success, -1 on error.

### dup

**Prototype:** `int dup(int oldfd);`

• **Description:** Duplicates a file descriptor to the lowest available descriptor.
• **Parameters:**
  - oldfd: File descriptor to duplicate.
• **Return Value:** New file descriptor on success, -1 on error.

### dup2

**Prototype:** `int dup2(int oldfd, int newfd);`

• **Description:** Duplicates oldfd to newfd, closing newfd if already open.
• **Parameters:**
  - oldfd: File descriptor to duplicate.
  - newfd: Target descriptor number.
• **Return Value:** newfd on success, -1 on error.

## Example Commands

1. **Creating a Sample File**
   ```bash
   echo "This is a test file with some data" > sample.txt
   ```
   Creates a sample file for testing.

2. **Creating a Data File for Parsing**
   ```bash
   echo "Item: 42 3.14 Widget" > data.txt
   ```
   Creates a file with formatted data for parsing with sscanf.

3. **Creating a Symbolic Link**
   ```bash
   ln -s sample.txt sample_link
   ```
   Creates a symbolic link to sample.txt.

4. **Checking File Metadata**
   ```bash
   ls -l sample.txt sample_link
   ```
   Displays metadata, distinguishing between the file and its symbolic link.

5. **Viewing File Descriptors**
   ```bash
   ls -l /proc/self/fd
   ```
   Lists open file descriptors for the current process.

## Sample Example: File Copy Program from Previous Lab

This example implements a file copy program from the last task of the previous lab. The program takes two command-line arguments: the source file path and the destination file path. It uses the open() system call to open the source file in read-only mode and the destination file in write-only mode (creating it if it doesn't exist). The read() system call reads chunks of data from the source file, and the write() system call writes these chunks to the destination file. The program includes error handling for all system calls and buffer overflow situations, closing files with close() after completion.

```c
// Header files
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h> // Various data types
#include <sys/stat.h>  // Permissions for user, group, other
#define BUFFER_SIZE 4096

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    // Open source file
    int source_fd = open(argv[1], O_RDONLY);
    if (source_fd == -1) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    // Open destination file (creating it if it doesn't exist)
    int dest_fd = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH);
    if (dest_fd == -1) {
        perror("Error opening destination file");
        close(source_fd);
        exit(EXIT_FAILURE);
    }

    // Copy data from source file to destination file
    char buffer[BUFFER_SIZE];
    ssize_t bytes_read, bytes_written;
    while ((bytes_read = read(source_fd, buffer, BUFFER_SIZE)) > 0) {
        bytes_written = write(dest_fd, buffer, bytes_read);
        if (bytes_written != bytes_read) {
            perror("Error writing to destination file");
            close(source_fd);
            close(dest_fd);
            exit(EXIT_FAILURE);
        }
    }

    if (bytes_read == -1) {
        perror("Error reading from source file");
        close(source_fd);
        close(dest_fd);
        exit(EXIT_FAILURE);
    }

    // Close files
    if (close(source_fd) == -1) {
        perror("Error closing source file");
        exit(EXIT_FAILURE);
    }
    if (close(dest_fd) == -1) {
        perror("Error closing destination file");
        exit(EXIT_FAILURE);
    }

    printf("File copied successfully.\n");
    return 0;
}
```

## Notes

• **Exit status checking** can be done using the following commands:
  ```bash
  ./your_program
  echo $?
  ```
  Note: `exit(1)` is equivalent to `exit(EXIT_FAILURE)`.

• **To redirect standard error stream to a file:**
  ```bash
  ./your_program 2> error.log
  ```
  This command runs your program and redirects error messages (stderr) to error.log. Inspect error.log to view errors.

• **To see both program output and standard error on the terminal:**
  ```bash
  ./your_program 2>&1
  ```