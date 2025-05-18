# System Programming Lab #8 - Command-Line Parsing and Process Creation

**Computer Science Department**  
**Engineering School**  
**Central Asian University**  
**Spring 2025**

## Objective

The objective of this lab is to provide students with hands-on experience in:

• Parsing command-line arguments using `getopt()` and `getopt_long()` in C programs.
• Understanding and implementing robust command-line options for real-world applications.
• Creating and managing child processes using `fork()` and using system calls (e.g., `write()`) to record results.

## Practice Tasks

Practice the two programs discussed during the lecture from the Linux Environment chapter:

• Writing your own function to get command-line options and arguments manually.
• Using Linux `getopt()` (and later `getopt_long()`) for enhanced option parsing. Experiment with changes to see the differences.

## Tasks

### Task 1: Handling Command-Line Arguments

Write a C program that:

1. Checks if exactly 2 arguments (including the program name) are passed.
2. Prints a usage message if the argument count is incorrect.
3. Processes each argument:
   • If an argument starts with `-`, print it as an option and process it using a function.
   • Otherwise, treat it as a regular argument and display it.
4. Implements the `process_option()` function to handle:
   • `-help`: Displays help information.
   • `-version`: Prints version details.
   • `-info`: Shows relevant information.

#### Solution for Task 1

```c
#include <stdio.h>
#include <string.h>

void process_option(const char *option) {
    if (strcmp(option, "-help") == 0) {
        printf("Help Information:\n");
        printf("This program demonstrates basic command-line argument handling.\n");
        printf("Usage: ./task1 [OPTION or ARGUMENT]\n");
        printf("Options:\n");
        printf("  -help     Display this help information\n");
        printf("  -version  Display version information\n");
        printf("  -info     Display relevant information\n");
    } else if (strcmp(option, "-version") == 0) {
        printf("Version Information:\n");
        printf("Command-Line Arguments Handler v1.0\n");
        printf("Created for System Programming Lab 8\n");
    } else if (strcmp(option, "-info") == 0) {
        printf("Information:\n");
        printf("This program was created as part of Lab 8 for the System Programming course.\n");
        printf("It demonstrates how to parse and handle command-line arguments manually.\n");
    } else {
        printf("Unknown option: %s\n", option);
        printf("Use -help for available options\n");
    }
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
        printf("Usage: %s [OPTION or ARGUMENT]\n", argv[0]);
        printf("Please provide exactly one argument\n");
        printf("Use -help for more information\n");
        return 1;
    }

    if (argv[1][0] == '-') {
        printf("Option: %s\n", argv[1]);
        process_option(argv[1]);
    } else {
        printf("Argument: %s\n", argv[1]);
    }

    return 0;
}
```

#### Output from Task 1 Execution

**Compilation:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % gcc -o task1 task1.c
```

**Test 1: No arguments**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1
Usage: ./task1 [OPTION or ARGUMENT]
Please provide exactly one argument
Use -help for more information
```

**Test 2: With -help option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 -help
Option: -help
Help Information:
This program demonstrates basic command-line argument handling.
Usage: ./task1 [OPTION or ARGUMENT]
Options:
  -help     Display this help information
  -version  Display version information
  -info     Display relevant information
```

**Test 3: With -version option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 -version
Option: -version
Version Information:
Command-Line Arguments Handler v1.0
Created for System Programming Lab 8
```

**Test 4: With -info option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 -info
Option: -info
Information:
This program was created as part of Lab 8 for the System Programming course.
It demonstrates how to parse and handle command-line arguments manually.
```

**Test 5: With unknown option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 -unknown
Option: -unknown
Unknown option: -unknown
Use -help for available options
```

**Test 6: With regular argument**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 argument
Argument: argument
```

**Test 7: With too many arguments**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task1 arg1 arg2
Usage: ./task1 [OPTION or ARGUMENT]
Please provide exactly one argument
Use -help for more information
```

### Task 2: Parsing Options with getopt()

Write a C program that:

1. Uses `getopt()` to parse command-line options.
2. Takes two options:
   • `-n` followed by an integer N.
   • `-m` followed by an integer M.
3. Computes:
   • The sum of the first N natural numbers (i.e., 1 + 2 + ... + N).
   • The product of the first M natural numbers (i.e., 1 × 2 × ... × M).
4. Prints a usage message if the required flags are missing.

#### Solution for Task 2

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int sum_of_natural_numbers(int n) {
    int sum = 0;
    for (int i = 1; i <= n; i++) {
        sum += i;
    }
    return sum;
}

long product_of_natural_numbers(int m) {
    long product = 1;
    for (int i = 1; i <= m; i++) {
        product *= i;
    }
    return product;
}

int main(int argc, char *argv[]) {
    int opt;
    int n_flag = 0, m_flag = 0;
    int n_value, m_value;
    
    while ((opt = getopt(argc, argv, "n:m:")) != -1) {
        switch (opt) {
            case 'n':
                n_flag = 1;
                n_value = atoi(optarg);
                break;
            case 'm':
                m_flag = 1;
                m_value = atoi(optarg);
                break;
            default:
                fprintf(stderr, "Usage: %s -n <integer> -m <integer>\n", argv[0]);
                return 1;
        }
    }
    
    if (!n_flag || !m_flag) {
        fprintf(stderr, "Error: Both -n and -m options are required\n");
        fprintf(stderr, "Usage: %s -n <integer> -m <integer>\n", argv[0]);
        return 1;
    }
    
    if (n_value <= 0 || m_value <= 0) {
        fprintf(stderr, "Error: N and M must be positive integers\n");
        return 1;
    }
    
    int sum = sum_of_natural_numbers(n_value);
    long product = product_of_natural_numbers(m_value);
    
    printf("Sum of first %d natural numbers: %d\n", n_value, sum);
    printf("Product of first %d natural numbers: %ld\n", m_value, product);
    
    return 0;
}
```

#### Output from Task 2 Execution

**Compilation:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % gcc -o task2 task2.c
```

**Test 1: No arguments**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2
Error: Both -n and -m options are required
Usage: ./task2 -n <integer> -m <integer>
```

**Test 2: Only -n option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -n 5
Error: Both -n and -m options are required
Usage: ./task2 -n <integer> -m <integer>
```

**Test 3: Only -m option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -m 4
Error: Both -n and -m options are required
Usage: ./task2 -n <integer> -m <integer>
```

**Test 4: Invalid value for -n**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -n -5 -m 3
Error: N and M must be positive integers
```

**Test 5: Valid input (small numbers)**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -n 5 -m 4
Sum of first 5 natural numbers: 15
Product of first 4 natural numbers: 24
```

**Test 6: Valid input (larger numbers)**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -n 10 -m 5
Sum of first 10 natural numbers: 55
Product of first 5 natural numbers: 120
```

**Test 7: Options in different order**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task2 -m 6 -n 8
Sum of first 8 natural numbers: 36
Product of first 6 natural numbers: 720
```

**Date of execution:** Sun May 18 21:35:18 +05 2025

### Task 3: File Encryption/Decryption using getopt()

Write a **C program** that encrypts or decrypts a file using **XOR encryption**. Your program should:

1. Use `getopt()` to handle command-line options:
   • `-E <input_file>` to encrypt the file.
   • `-D <input_file>` to decrypt the file.
   • `-o <output_file>` to specify the output file.
   • `-k <key>` (Optional) to specify an encryption key; defaults to 'K' if not provided.

2. Implement XOR-based encryption/decryption where:
   • Each character is XORed with the given key.
   • The same function is used for both encryption and decryption.

3. Ensure proper error handling:
   • Display an error message if incorrect options are provided.
   • Handle file errors gracefully.

#### Solution for Task 3

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

void process_file(FILE *input, FILE *output, char key) {
    int ch;
    while ((ch = fgetc(input)) != EOF) {
        fputc(ch ^ key, output);
    }
}

int main(int argc, char *argv[]) {
    int opt;
    char *input_file = NULL;
    char *output_file = NULL;
    char key = 'K'; 
    int encrypt_flag = 0;
    int decrypt_flag = 0;
    
    while ((opt = getopt(argc, argv, "E:D:o:k:")) != -1) {
        switch (opt) {
            case 'E':
                encrypt_flag = 1;
                input_file = optarg;
                break;
            case 'D':
                decrypt_flag = 1;
                input_file = optarg;
                break;
            case 'o':
                output_file = optarg;
                break;
            case 'k':
                key = optarg[0]; 
                break;
            default:
                fprintf(stderr, "Usage: %s -E|-D <input_file> -o <output_file> [-k key]\n", argv[0]);
                return 1;
        }
    }
    
    if (encrypt_flag && decrypt_flag) {
        fprintf(stderr, "Error: Specify either -E for encryption or -D for decryption, not both.\n");
        return 1;
    }
    
    if (!encrypt_flag && !decrypt_flag) {
        fprintf(stderr, "Error: You must specify either -E for encryption or -D for decryption.\n");
        return 1;
    }
    
    if (input_file == NULL) {
        fprintf(stderr, "Error: Input file not specified.\n");
        return 1;
    }
    
    if (output_file == NULL) {
        fprintf(stderr, "Error: Output file not specified. Use -o option.\n");
        return 1;
    }
    
    FILE *infile = fopen(input_file, "rb");
    if (infile == NULL) {
        fprintf(stderr, "Error: Could not open input file '%s'.\n", input_file);
        return 1;
    }
    
    FILE *outfile = fopen(output_file, "wb");
    if (outfile == NULL) {
        fprintf(stderr, "Error: Could not open output file '%s'.\n", output_file);
        fclose(infile);
        return 1;
    }
    
    process_file(infile, outfile, key);
    
    fclose(infile);
    fclose(outfile);
    
    if (encrypt_flag) {
        printf("File '%s' encrypted to '%s' using key '%c'.\n", input_file, output_file, key);
    } else {
        printf("File '%s' decrypted to '%s' using key '%c'.\n", input_file, output_file, key);
    }
    
    return 0;
}
```

#### Output from Task 3 Execution

**Compilation:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % gcc -o task3 task3.c
```

**Creating a test file:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % echo "This is a test message for XOR encryption. System Programming Lab 8." > input.txt
```

**Test 1: No arguments**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3
Error: You must specify either -E for encryption or -D for decryption.
```

**Test 2: Only -E without output file**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -E input.txt
Error: Output file not specified. Use -o option.
```

**Test 3: Both -E and -D options**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -E input.txt -D input.txt -o output.txt
Error: Specify either -E for encryption or -D for decryption, not both.
```

**Test 4: Encrypt with default key**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -E input.txt -o encrypted.txt
File 'input.txt' encrypted to 'encrypted.txt' using key 'K'.
```

**Test 5: Decrypt with default key**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -D encrypted.txt -o decrypted1.txt
File 'encrypted.txt' decrypted to 'decrypted1.txt' using key 'K'.
```

**Test 6: Encrypt with custom key**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -E input.txt -o encrypted_X.txt -k X
File 'input.txt' encrypted to 'encrypted_X.txt' using key 'X'.
```

**Test 7: Decrypt with custom key**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -D encrypted_X.txt -o decrypted_X.txt -k X
File 'encrypted_X.txt' decrypted to 'decrypted_X.txt' using key 'X'.
```

**Test 8: Decrypt with wrong key**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task3 -D encrypted.txt -o decrypted_wrong.txt -k Z
File 'encrypted.txt' decrypted to 'decrypted_wrong.txt' using key 'Z'.
```

**Verification of encryption/decryption**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat input.txt
This is a test message for XOR encryption. System Programming Lab 8.

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat decrypted1.txt
This is a test message for XOR encryption. System Programming Lab 8.

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat decrypted_wrong.txt
Eyxb1xb1p1etbe1|tbbpvt1w~c1I^C1trchaex~?1Bhbet|1Ac~vcp||xv1]ps1)?%
```

**Date of execution:** Sun May 18 21:38:50 +05 2025

### Task 4: Using fork() for Parallel Execution

Write a C program that:

1. Uses `getopt()` to parse options:
   • `-n` followed by an integer N.
   • `-m` followed by an integer M.
   • `-f` followed by a filename to save results.

2. Creates two child processes using `fork()`:
   • One child computes the sum of N and M.
   • Another child computes the product of N and M.

3. Saves results to the specified file using the `write()` system call.

4. Displays a usage message if any required option is missing.

#### Solution for Task 4

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <fcntl.h>

int main(int argc, char *argv[]) {
    int opt;
    int n_flag = 0, m_flag = 0, f_flag = 0;
    int n_value, m_value;
    char *filename = NULL;
    
    while ((opt = getopt(argc, argv, "n:m:f:")) != -1) {
        switch (opt) {
            case 'n':
                n_flag = 1;
                n_value = atoi(optarg);
                break;
            case 'm':
                m_flag = 1;
                m_value = atoi(optarg);
                break;
            case 'f':
                f_flag = 1;
                filename = optarg;
                break;
            default:
                fprintf(stderr, "Usage: %s -n <integer> -m <integer> -f <filename>\n", argv[0]);
                return 1;
        }
    }
    
    if (!n_flag || !m_flag || !f_flag) {
        fprintf(stderr, "Error: All options -n, -m, and -f are required\n");
        fprintf(stderr, "Usage: %s -n <integer> -m <integer> -f <filename>\n", argv[0]);
        return 1;
    }
    
    pid_t pid1, pid2;
    int status;
    
    pid1 = fork();
    
    if (pid1 < 0) {
        fprintf(stderr, "Error: Fork failed\n");
        return 1;
    } else if (pid1 == 0) {
        int sum = n_value + m_value;
        
        int fd = open(filename, O_WRONLY | O_CREAT | O_APPEND, 0644);
        if (fd < 0) {
            fprintf(stderr, "Error: Could not open file %s\n", filename);
            exit(1);
        }
        
        char buffer[100];
        int len = snprintf(buffer, sizeof(buffer), "Sum of %d and %d is %d\n", n_value, m_value, sum);
        write(fd, buffer, len);
        
        close(fd);
        exit(0);
    } else {
        pid2 = fork();
        
        if (pid2 < 0) {
            fprintf(stderr, "Error: Fork failed\n");
            return 1;
        } else if (pid2 == 0) {
            int product = n_value * m_value;
            
            int fd = open(filename, O_WRONLY | O_CREAT | O_APPEND, 0644);
            if (fd < 0) {
                fprintf(stderr, "Error: Could not open file %s\n", filename);
                exit(1);
            }
            
            char buffer[100];
            int len = snprintf(buffer, sizeof(buffer), "Product of %d and %d is %d\n", n_value, m_value, product);
            write(fd, buffer, len);
            
            close(fd);
            exit(0);
        } else {
            waitpid(pid1, &status, 0);
            waitpid(pid2, &status, 0);
            
            printf("Both child processes completed successfully.\n");
            printf("Results saved to %s\n", filename);
        }
    }
    
    return 0;
}
```

#### Output from Task 4 Execution

**Compilation:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % gcc -o task4 task4.c
```

**Test 1: No arguments**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4
Error: All options -n, -m, and -f are required
Usage: ./task4 -n <integer> -m <integer> -f <filename>
```

**Test 2: Missing -f option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4 -n 10 -m 5
Error: All options -n, -m, and -f are required
Usage: ./task4 -n <integer> -m <integer> -f <filename>
```

**Test 3: Missing -n option**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4 -m 5 -f results.txt
Error: All options -n, -m, and -f are required
Usage: ./task4 -n <integer> -m <integer> -f <filename>
```

**Test 4: All options provided**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4 -n 10 -m 5 -f results.txt
Both child processes completed successfully.
Results saved to results.txt
```

**Test 5: Different values**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4 -n 15 -m 7 -f results2.txt
Both child processes completed successfully.
Results saved to results2.txt
```

**Test 6: Options in different order**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % ./task4 -f results3.txt -n 8 -m 12
Both child processes completed successfully.
Results saved to results3.txt
```

**Verification of results:**
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat results.txt
Product of 10 and 5 is 50
Sum of 10 and 5 is 15

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat results2.txt
Sum of 15 and 7 is 22
Product of 15 and 7 is 105

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab8 % cat results3.txt
Sum of 8 and 12 is 20
Product of 8 and 12 is 96
```

**Date of execution:** Sun May 18 21:49:28 +05 2025