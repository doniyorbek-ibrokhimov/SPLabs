# Lab 2: Writing, Compiling, Running C Programs in LINUX, Creating Static Libraries and Exploring Redirections, Pipes and Advanced LINUX Commands for Scripting

**Muhammad Bilal Qureshi**  
Department of Computer Science  
School of Engineering  
**Report Submission Date:** 11th February 2025

## Objective
• To practice writing and compiling programs using the vi editor.
• To understand static library creation and linking in C programming.
• To explore redirection, piping, and advanced Linux commands for scripting and file management.

## Task 1
Write a simple C program using the vi editor to print:
• Your ID, Name, and marks of all subjects in an array.
• Ensure that all data is stored in appropriate variables or arrays.
• Familiarize yourself with vi commands from Lecture 1 and Lecture 2 slides.

### Implementation and Output

Below is the C code created for this task, along with the compilation and execution steps and their outputs.

**C Code (`task1_program.c`):**
```c
#include <stdio.h>

int main() {
    // Student Information
    char student_id[] = "210046";
    char student_name[] = "Doniyorbek Ibrokhimov";

    // Marks for 5 subjects
    char subjects[][50] = {
        "System Programming",
        "Data Structures",
        "Algorithms",
        "Operating Systems",
        "Computer Networks"
    };
    float marks[] = {85.5f, 90.0f, 78.5f, 92.0f, 88.0f};
    int num_subjects = sizeof(marks) / sizeof(marks[0]);

    // Print student ID and Name
    printf("Student ID: %s\n", student_id);
    printf("Student Name: %s\n", student_name);
    printf("\nMarks in Subjects:\n");

    // Print marks for each subject
    for (int i = 0; i < num_subjects; i++) {
        printf("%s: %.1f\n", subjects[i], marks[i]);
    }

    return 0;
}
```

**Compilation:**
```sh
d.ibrokhimov@ec2-100-20-3-127 Spring25 % gcc SP/labs/task1_program.c -o SP/labs/task1_executable
d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
```

**Execution and Output:**
```sh
d.ibrokhimov@ec2-100-20-3-127 Spring25 % ./SP/labs/task1_executable
Student ID: 210046
Student Name: Doniyorbek Ibrokhimov

Marks in Subjects:
System Programming: 85.5
Data Structures: 90.0
Algorithms: 78.5
Operating Systems: 92.0
Computer Networks: 88.0
d.ibrokhimov@ec2-100-20-3-127 Spring25 %
```

### Saving, Compiling, and Running
1. Press the `Esc` key to exit insert mode.
2. Save and exit the file using: `:wq`.
3. To compile the code, use the command: `gcc hello.c`.
4. Run the program using: `./a.out`.
5. Alternatively, compile the code with a custom output name: `gcc -o hello hello.c`.
6. Run the program with the custom name: `./hello`.

## Task 2
Repeat the task of creating a static library and linking it with a program as mentioned in the Lecture Slides (pages 43–45). Ensure you understand each step before proceeding to Task 3.

### Linking, Compiling, and Running
1. Compile the source files into object files:
   ```bash
   gcc -c fred.c bill.c
   ```
2. Create a static library called `libfoo.a`:
   ```bash
   ar crv libfoo.a bill.o fred.o
   ```
3. Use this library to link it to the program:
   ```bash
   gcc -o program program.o libfoo.a
   ```
4. Run the program: `./program`

### Practical Implementation and Output

Below are the contents of the source files created for this task and the terminal session showing the commands executed and their outputs. All commands were run from the `SP/labs/` directory.

**`foo.h` (Header File):**
```c
#ifndef FOO_H
#define FOO_H

void bill(char *message);
void fred(int times);

#endif
```

**`bill.c` (Library Source File):**
```c
#include <stdio.h>
#include "foo.h"

void bill(char *message) {
    printf("bill: Message received: %s\n", message);
}
```

**`fred.c` (Library Source File):**
```c
#include <stdio.h>
#include "foo.h"

void fred(int times) {
    printf("fred: I will say hello %d time(s).\n", times);
    for (int i = 0; i < times; i++) {
        printf("fred: Hello! (%d/%d)\n", i + 1, times);
    }
}
```

**`program.c` (Main Program):**
```c
#include "foo.h"

int main() {
    bill("Greetings from the main program!");
    fred(3);
    return 0;
}
```

**Terminal Session:**

1.  **Change directory to `SP/labs` (from project root `/Users/d.ibrokhimov/Documents/Spring25`):**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 Spring25 % cd SP/labs
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

2.  **Compile `fred.c` and `bill.c` into object files:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -c fred.c bill.c
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

3.  **Create static library `libfoo.a`:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ar crv libfoo.a bill.o fred.o
    a - bill.o
    a - fred.o
    d.ibrokhimov@ec2-100-20-3-127 labs %
    ```

4.  **Compile `program.c` into an object file `program.o`:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -c program.c -o program.o
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

5.  **Link `program.o` with `libfoo.a` to create the executable `program`:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -o program program.o libfoo.a
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

6.  **Run the program `./program`:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ./program
    bill: Message received: Greetings from the main program!
    fred: I will say hello 3 time(s).
    fred: Hello! (1/3)
    fred: Hello! (2/3)
    fred: Hello! (3/3)
    d.ibrokhimov@ec2-100-20-3-127 labs %
    ```
7.  **Change directory back to project root:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cd ../..
    d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
    ```

## Task 3
Create a static library named `libYourLastName.a`. This library must include the following matrix functions:

1. **`void YourName_Get(double Mat[][3])`**  
   Reads data for a 3x3 matrix.

2. **`void YourName_Show(double Mat[][3])`**  
   Displays a 3x3 matrix.

3. **`void YourName_Add(double MatA[][3], double MatB[][3], double MatC[][3])`**  
   Adds two 3x3 matrices (Mat A and Mat B) and saves the result in Mat C.

4. **`void YourName_Mul(double MatA[][3], double MatB[][3], double MatC[][3])`**  
   Multiplies two 3x3 matrices (Mat A and Mat B) and saves the result in Mat C.

5. **`void YourName_Inv(double MatA[][3], double MatB[][3])`**  
   Computes the inverse of a 3x3 matrix (Mat A) and saves the result in Mat B.

6. **`double YourName_Mod(double MatA[][3])`**  
   Calculates and returns the modulus of a 3x3 matrix (Mat A).

Write the steps to compile and use the library, then call these functions in your main program to demonstrate their functionality.

### Implementation Details

The following files were created in the `SP/labs/` directory:
- `Doniyor_matrix.h`: Header file for the matrix library.
- `Doniyor_matrix.c`: Source code for the matrix library functions.
- `matrix_demo.c`: Main program to demonstrate the library.

**`Doniyor_matrix.h`**
```c
#ifndef DONIYOR_MATRIX_H
#define DONIYOR_MATRIX_H

#define MATRIX_SIZE 3

void Doniyor_Get(double Mat[][MATRIX_SIZE]);
void Doniyor_Show(double Mat[][MATRIX_SIZE]);
void Doniyor_Add(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE], double MatC[][MATRIX_SIZE]);
void Doniyor_Mul(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE], double MatC[][MATRIX_SIZE]);
void Doniyor_Inv(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE]);
double Doniyor_Mod(double MatA[][MATRIX_SIZE]);

#endif
```

**`Doniyor_matrix.c`**
```c
#include <stdio.h>
#include <math.h>
#include "Doniyor_matrix.h"

void Doniyor_Get(double Mat[][MATRIX_SIZE]) {
    printf("Enter the %dx%d matrix elements:\n", MATRIX_SIZE, MATRIX_SIZE);
    for (int i = 0; i < MATRIX_SIZE; i++) {
        for (int j = 0; j < MATRIX_SIZE; j++) {
            printf("Element [%d][%d]: ", i, j);
            scanf("%lf", &Mat[i][j]);
        }
    }
}

void Doniyor_Show(double Mat[][MATRIX_SIZE]) {
    printf("Matrix:\n");
    for (int i = 0; i < MATRIX_SIZE; i++) {
        for (int j = 0; j < MATRIX_SIZE; j++) {
            printf("%.2f\t", Mat[i][j]);
        }
        printf("\n");
    }
}

void Doniyor_Add(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE], double MatC[][MATRIX_SIZE]) {
    for (int i = 0; i < MATRIX_SIZE; i++) {
        for (int j = 0; j < MATRIX_SIZE; j++) {
            MatC[i][j] = MatA[i][j] + MatB[i][j];
        }
    }
}

void Doniyor_Mul(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE], double MatC[][MATRIX_SIZE]) {
    for (int i = 0; i < MATRIX_SIZE; i++) {
        for (int j = 0; j < MATRIX_SIZE; j++) {
            MatC[i][j] = 0;
            for (int k = 0; k < MATRIX_SIZE; k++) {
                MatC[i][j] += MatA[i][k] * MatB[k][j];
            }
        }
    }
}

double Doniyor_Mod(double Mat[][MATRIX_SIZE]) {
    double det = 0;
    if (MATRIX_SIZE == 3) {
        det = Mat[0][0] * (Mat[1][1] * Mat[2][2] - Mat[1][2] * Mat[2][1]) -
              Mat[0][1] * (Mat[1][0] * Mat[2][2] - Mat[1][2] * Mat[2][0]) +
              Mat[0][2] * (Mat[1][0] * Mat[2][1] - Mat[1][1] * Mat[2][0]);
    } else {
        printf("Modulus calculation currently only supported for 3x3 matrices.\n");
        return NAN;
    }
    return det;
}

void Doniyor_Inv(double MatA[][MATRIX_SIZE], double MatB[][MATRIX_SIZE]) {
    if (MATRIX_SIZE != 3) {
        printf("Inverse calculation currently only supported for 3x3 matrices.\n");
        for(int i=0; i<MATRIX_SIZE; ++i) for(int j=0; j<MATRIX_SIZE; ++j) MatB[i][j] = NAN;
        return;
    }

    double det = Doniyor_Mod(MatA);
    if (fabs(det) < 1e-9) { 
        printf("Matrix is singular, inverse does not exist.\n");
        for(int i=0; i<MATRIX_SIZE; ++i) for(int j=0; j<MATRIX_SIZE; ++j) MatB[i][j] = NAN;
        return;
    }

    double invDet = 1.0 / det;

    MatB[0][0] = (MatA[1][1] * MatA[2][2] - MatA[1][2] * MatA[2][1]) * invDet;
    MatB[0][1] = (MatA[0][2] * MatA[2][1] - MatA[0][1] * MatA[2][2]) * invDet;
    MatB[0][2] = (MatA[0][1] * MatA[1][2] - MatA[0][2] * MatA[1][1]) * invDet;
    MatB[1][0] = (MatA[1][2] * MatA[2][0] - MatA[1][0] * MatA[2][2]) * invDet;
    MatB[1][1] = (MatA[0][0] * MatA[2][2] - MatA[0][2] * MatA[2][0]) * invDet;
    MatB[1][2] = (MatA[0][2] * MatA[1][0] - MatA[0][0] * MatA[1][2]) * invDet;
    MatB[2][0] = (MatA[1][0] * MatA[2][1] - MatA[1][1] * MatA[2][0]) * invDet;
    MatB[2][1] = (MatA[0][1] * MatA[2][0] - MatA[0][0] * MatA[2][1]) * invDet;
    MatB[2][2] = (MatA[0][0] * MatA[1][1] - MatA[0][1] * MatA[1][0]) * invDet;
}
```

**`matrix_demo.c` (Main Program)**
```c
#include <stdio.h>
#include "Doniyor_matrix.h"

int main() {
    double mat1[MATRIX_SIZE][MATRIX_SIZE];
    double mat2[MATRIX_SIZE][MATRIX_SIZE];
    double mat_sum[MATRIX_SIZE][MATRIX_SIZE];
    double mat_prod[MATRIX_SIZE][MATRIX_SIZE];
    double mat_inv1[MATRIX_SIZE][MATRIX_SIZE];
    double mod_val;

    printf("--- Matrix 1 Input ---\n");
    Doniyor_Get(mat1);
    printf("\n--- Matrix 2 Input ---\n");
    Doniyor_Get(mat2);

    printf("\n--- Matrix 1 ---\n");
    Doniyor_Show(mat1);
    printf("\n--- Matrix 2 ---\n");
    Doniyor_Show(mat2);

    printf("\n--- Sum of Matrix 1 and Matrix 2 ---\n");
    Doniyor_Add(mat1, mat2, mat_sum);
    Doniyor_Show(mat_sum);

    printf("\n--- Product of Matrix 1 and Matrix 2 ---\n");
    Doniyor_Mul(mat1, mat2, mat_prod);
    Doniyor_Show(mat_prod);

    printf("\n--- Modulus (Determinant) of Matrix 1 ---\n");
    mod_val = Doniyor_Mod(mat1);
    printf("Modulus: %.2f\n", mod_val);

    printf("\n--- Inverse of Matrix 1 ---\n");
    Doniyor_Inv(mat1, mat_inv1);
    Doniyor_Show(mat_inv1);
    
    printf("\n--- Example: Identity Matrix for Inverse and Modulus Test ---\n");
    double identity[MATRIX_SIZE][MATRIX_SIZE] = {{1,0,0},{0,1,0},{0,0,1}};
    double identity_inv[MATRIX_SIZE][MATRIX_SIZE];
    printf("--- Identity Matrix ---\n");
    Doniyor_Show(identity);
    printf("Modulus of Identity: %.2f\n", Doniyor_Mod(identity));
    printf("Inverse of Identity:\n");
    Doniyor_Inv(identity, identity_inv);
    Doniyor_Show(identity_inv);

    return 0;
}
```

### Compilation, Linking, and Execution Steps

All commands are run from the `SP/labs/` directory.

1.  **Change directory to `SP/labs` (from project root):**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 Spring25 % cd SP/labs
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

2.  **Compile `Doniyor_matrix.c` to an object file:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -c Doniyor_matrix.c -o Doniyor_matrix.o
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

3.  **Create the static library `libDoniyor.a`:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ar crv libDoniyor.a Doniyor_matrix.o
    a - Doniyor_matrix.o
    d.ibrokhimov@ec2-100-20-3-127 labs %
    ```

4.  **Compile `matrix_demo.c` to an object file:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -c matrix_demo.c -o matrix_demo.o
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

5.  **Link `matrix_demo.o` with `libDoniyor.a` to create the executable (linking math library with `-lm`):**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % gcc -o matrix_demo_app matrix_demo.o libDoniyor.a -lm
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

6.  **Run the program `./matrix_demo_app` with piped input:**
    Input provided (each number on a new line for `scanf`):
    Matrix 1: 1 2 3 / 4 5 6 / 7 8 9
    Matrix 2: 9 8 7 / 6 5 4 / 3 2 1

    Command:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % printf '1\n2\n3\n4\n5\n6\n7\n8\n9\n9\n8\n7\n6\n5\n4\n3\n2\n1\n' | ./matrix_demo_app
    ```

    Output:
    ```
    --- Matrix 1 Input ---
    Enter the 3x3 matrix elements:
    Element [0][0]: Element [0][1]: Element [0][2]: Element [1][0]: Element [1][1]: Element [1][2]: Element [2][0]: Element [2][1]: Element [2][2]: 
    --- Matrix 2 Input ---
    Enter the 3x3 matrix elements:
    Element [0][0]: Element [0][1]: Element [0][2]: Element [1][0]: Element [1][1]: Element [1][2]: Element [2][0]: Element [2][1]: Element [2][2]: 
    --- Matrix 1 ---
    Matrix:
    1.00	2.00	3.00	
    4.00	5.00	6.00	
    7.00	8.00	9.00	
    
    --- Matrix 2 ---
    Matrix:
    9.00	8.00	7.00	
    6.00	5.00	4.00	
    3.00	2.00	1.00	
    
    --- Sum of Matrix 1 and Matrix 2 ---
    Matrix:
    10.00	10.00	10.00	
    10.00	10.00	10.00	
    10.00	10.00	10.00	
    
    --- Product of Matrix 1 and Matrix 2 ---
    Matrix:
    30.00	24.00	18.00	
    84.00	69.00	54.00	
    138.00	114.00	90.00	
    
    --- Modulus (Determinant) of Matrix 1 ---
    Modulus: 0.00
    
    --- Inverse of Matrix 1 ---
    Matrix is singular, inverse does not exist.
    Matrix:
    nan	nan	nan	
    nan	nan	nan	
    nan	nan	nan	
    
    --- Example: Identity Matrix for Inverse and Modulus Test ---
    --- Identity Matrix ---
    Matrix:
    1.00	0.00	0.00	
    0.00	1.00	0.00	
    0.00	0.00	1.00	
    Modulus of Identity: 1.00
    Inverse of Identity:
    Matrix:
    1.00	0.00	0.00	
    0.00	1.00	0.00	
    0.00	0.00	1.00	
    ```

7.  **Change directory back to project root:**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cd ../..
    d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
    ```

## Task 4
Work with redirection and file manipulation using the following commands:

### Command Outputs

All commands were run from the `SP/labs/` directory.

1.  **`$ ls -al | more`** (executed as `ls -al | cat`)
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ls -al | cat
    total 2048
    drwxr-xr-x@ 31 d.ibrokhimov  staff     992 May 18 19:38 .
    drwxr-xr-x@  8 d.ibrokhimov  staff     256 May 17 03:41 ..
    -rw-r--r--@  1 d.ibrokhimov  staff    3015 May 18 19:36 Doniyor_matrix.c
    -rw-r--r--@  1 d.ibrokhimov  staff     499 May 18 19:36 Doniyor_matrix.h
    -rw-r--r--@  1 d.ibrokhimov  staff    3288 May 18 19:37 Doniyor_matrix.o
    -rw-r--r--@  1 d.ibrokhimov  staff    3304 May 18 19:32 Ibrokhimov_matrix.o
    -rwxr-xr-x@  1 d.ibrokhimov  staff   33560 May 18 19:17 a.out
    -rw-r--r--@  1 d.ibrokhimov  staff     119 May 18 19:29 bill.c
    # ... (output truncated for brevity) ...
    -rwxr-xr-x@  1 d.ibrokhimov  staff   33496 May 18 19:24 program
    -rw-r--r--@  1 d.ibrokhimov  staff     237 May 18 19:29 program.c
    -rw-r--r--@  1 d.ibrokhimov  staff     800 May 18 19:24 program.o
    d.ibrokhimov@ec2-100-20-3-127 labs %
    ```

2.  **`$ man bash | col -b | lpr`** (executed as `man bash | col -b > man_bash_output.txt`, then `cat man_bash_output.txt`)
    Command to create file:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % man bash | col -b > man_bash_output.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `man_bash_output.txt` (truncated):
    ```
    BASH(1)                               General Commands Manual                              BASH(1)

    NAME
           bash - GNU Bourne-Again SHell

    SYNOPSIS
           bash [options] [file]

    COPYRIGHT
           Bash is Copyright (C) 1989-2007 by the Free Software Foundation, Inc.
    # ... (output heavily truncated due to length) ...
    GNU Bash-3.2                   2006 September 28                       BASH(1)
    ```

3.  **`$ bash --version`**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % bash --version
    GNU bash, version 3.2.57(1)-release (arm64-apple-darwin24)
    Copyright (C) 2007 Free Software Foundation, Inc.
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

4.  **`$ /bin/bash --version`**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % /bin/bash --version
    GNU bash, version 3.2.57(1)-release (arm64-apple-darwin24)
    Copyright (C) 2007 Free Software Foundation, Inc.
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

5.  **`$ /bin/zsh --version`**
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % /bin/zsh --version
    zsh 5.9 (arm64-apple-darwin24.0)
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

6.  **`$ ls -l > lsoutput.txt`**
    Command to create file (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ls -l > lsoutput.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `lsoutput.txt` (shown by `cat lsoutput.txt`):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat lsoutput.txt
    total 2544
    -rw-r--r--@ 1 d.ibrokhimov  staff    3015 May 18 19:36 Doniyor_matrix.c
    # ... (output truncated for brevity, matches ls -l of the directory) ...
    -rw-r--r--@ 1 d.ibrokhimov  staff     800 May 18 19:24 program.o
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

7.  **`$ ps >> lsoutput.txt`**
    Command to append to file (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ps >> lsoutput.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `lsoutput.txt` after append (shown by `cat lsoutput.txt`):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat lsoutput.txt
    total 2544
    -rw-r--r--@ 1 d.ibrokhimov  staff    3015 May 18 19:36 Doniyor_matrix.c
    # ... (ls -l output) ...
    -rw-r--r--@ 1 d.ibrokhimov  staff     800 May 18 19:24 program.o
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:01.07 docker run -it ubuntu /bin/bash
    27400 ttys006    0:00.03 /bin/zsh -il
    28994 ttys008    0:00.14 /bin/zsh -il
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

8.  **`$ kill -HUP 1234 > killout.txt 2> killerr.txt`**
    Command (no direct terminal output, exit status 1 indicated an error):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % kill -HUP 1234 > killout.txt 2> killerr.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `killout.txt`:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat killout.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `killerr.txt`:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat killerr.txt
    kill: kill 1234 failed: no such process
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

9.  **`$ kill -1 1234 > killouterr.txt 2>&1`**
    Command (no direct terminal output, exit status 1 indicated an error):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % kill -1 1234 > killouterr.txt 2>&1
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `killouterr.txt`:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat killouterr.txt
    kill: kill 1234 failed: no such process
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

10. **`$ kill -1 1234 > /dev/null 2>&1`**
    Command (no terminal output as all redirected to /dev/null, exit status 1 indicated an error):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % kill -1 1234 > /dev/null 2>&1
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

11. **`$ more < lsoutput.txt`** (executed as `cat < lsoutput.txt`)
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat < lsoutput.txt
    total 2544
    -rw-r--r--@ 1 d.ibrokhimov  staff    3015 May 18 19:36 Doniyor_matrix.c
    # ... (output is the same content of lsoutput.txt as shown in step 7) ...
    28994 ttys008    0:00.14 /bin/zsh -il
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

12. **`$ ps > psout.txt`**
    Command (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ps > psout.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `psout.txt`:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat psout.txt
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:01.08 docker run -it ubuntu /bin/bash
    27400 ttys006    0:00.03 /bin/zsh -il
    28994 ttys008    0:00.16 /bin/zsh -il
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

13. **`$ sort psout.txt > pssort.out`**
    Command (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % sort psout.txt > pssort.out
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `pssort.out`:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat pssort.out
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:01.08 docker run -it ubuntu /bin/bash
    27400 ttys006    0:00.03 /bin/zsh -il
    28994 ttys008    0:00.16 /bin/zsh -il
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

14. **`$ ps | sort > pssort.out`**
    Command (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ps | sort > pssort.out
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `pssort.out` (overwritten):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat pssort.out
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:01.09 docker run -it ubuntu /bin/bash
    27400 ttys006    0:00.03 /bin/zsh -il
    28994 ttys008    0:00.17 /bin/zsh -il
    30547 ttys008    0:00.00 sort
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

15. **`$ ps | sort | more`** (executed as `ps | sort | cat`)
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ps | sort | cat
      PID TTY           TIME CMD
    20403 ttys000    0:00.15 -zsh -g --no_rcs
    26420 ttys000    0:01.09 docker run -it ubuntu /bin/bash
    27400 ttys006    0:00.03 /bin/zsh -il
    28994 ttys008    0:00.18 /bin/zsh -il
    30582 ttys008    0:00.00 sort
    30583 ttys008    0:00.00 cat
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

16. **`$ ps -xo comm | sort | uniq | grep -v sh | more`** (executed as `ps -xo comm | sort | uniq | grep -v sh | cat`)
    Output (truncated due to length):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % ps -xo comm | sort | uniq | grep -v sh | cat
    /Applications/AlDente.app/Contents/MacOS/AlDente
    /Applications/Arc.app/Contents/Frameworks/ArcCore.framework/Helpers/Browser Helper (GPU).app/Contents/MacOS/Browser Helper (GPU)
    # ... (many lines of process commands) ...
    cat
    docker
    grep
    ps
    sort
    uniq
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```

17. **`$ cat mydata.txt | sort | uniq > mydata.txt`**
    First, create `mydata.txt` with content:
    ```
    apple
    banana
    orange
    apple
    banana
    banana
    grape
    ```
    Command execution (no direct terminal output):
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat mydata.txt | sort | uniq > mydata.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    Content of `mydata.txt` after the command:
    ```sh
    d.ibrokhimov@ec2-100-20-3-127 labs % cat mydata.txt
    d.ibrokhimov@ec2-100-20-3-127 labs % 
    ```
    *(Note: `mydata.txt` becomes empty. This happens because the shell prepares the redirection by opening `mydata.txt` for writing (truncating it) before `cat mydata.txt` on the left side of the pipe reads its original content.)*

Change directory back to project root:
```sh
d.ibrokhimov@ec2-100-20-3-127 labs % cd ../..
d.ibrokhimov@ec2-100-20-3-127 Spring25 %
```

## Task 5
Installation related commands for Ubuntu/macOS:

### Ubuntu Commands:
• `$ sudo apt update` (To update the list of packages.)
• `$ sudo apt install bash` (On personal systems only.)

**1. `$ brew --version`**
   (For macOS, check if Homebrew is installed.)
   ```sh
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % brew --version
   Homebrew 4.5.2
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
   ```

**2. If Homebrew is not installed, use:**
   (This command was not executed as Homebrew is already installed.)
   ```bash
   $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

**3. To update Homebrew:**
   **`$ brew update`**
   ```sh
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % brew update
   ==> Updating Homebrew...
   Updated 1 tap (homebrew/cask).
   ==> New Casks
   bison-wallet                                           opensuperwhisper
   ==> Outdated Formulae
   gettext        harfbuzz       libdeflate     livekit-cli    pixman         snappy         unbound
   gh             heroku         libpng         mpdecimal      ruby           sqlite
   gzip           icu4c@76       libpq          openssl@3      sdl2           swiftformat
   ==> Outdated Casks
   anydesk

   You have 19 outdated formulae and 1 outdated cask installed.
   You can upgrade them with brew upgrade
   or list them with brew outdated.
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
   ```

**4. And install Bash using:**
   **`$ brew install bash`**
   ```sh
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % brew install bash
   ==> Downloading https://ghcr.io/v2/homebrew/core/bash/manifests/5.2.37-2
   ##################################################################################################### 100.0%
   ==> Fetching dependencies for bash: ncurses
   ==> Downloading https://ghcr.io/v2/homebrew/core/ncurses/manifests/6.5
   ##################################################################################################### 100.0%
   ==> Fetching ncurses
   ==> Downloading https://ghcr.io/v2/homebrew/core/ncurses/blobs/sha256:4a529cb864994c26766c55ae8e506297523b36
   ##################################################################################################### 100.0%
   ==> Fetching bash
   ==> Downloading https://ghcr.io/v2/homebrew/core/bash/blobs/sha256:e77d408d550e8e9f6669abf16759e35d0b867fe21
   ##################################################################################################### 100.0%
   ==> Installing dependencies for bash: ncurses
   ==> Installing bash dependency: ncurses
   ==> Downloading https://ghcr.io/v2/homebrew/core/ncurses/manifests/6.5
   Already downloaded: /Users/d.ibrokhimov/Library/Caches/Homebrew/downloads/b2f8ce4e08b15832435170efd265346225ee388d5baab3d5c50e3a77c5673b5a--ncurses-6.5.bottle_manifest.json
   ==> Pouring ncurses--6.5.arm64_sequoia.bottle.tar.gz
   🍺  /opt/homebrew/Cellar/ncurses/6.5: 4,052 files, 9.9MB
   ==> Installing bash
   ==> Pouring bash--5.2.37.arm64_sequoia.bottle.2.tar.gz
   🍺  /opt/homebrew/Cellar/bash/5.2.37: 163 files, 12.4MB
   ==> Running `brew cleanup bash`...
   Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
   Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
   d.ibrokhimov@ec2-100-20-3-127 Spring25 % 
   ```

## Online Resources
• Access Linux terminal online: https://www.labex.io/learn
• Access Linux using CoCalc: https://www.cocalc.com

## Notes for macOS Users
• Some commands like `apt`, `dpkg`, and `rpm` are not available on macOS. Use `brew` for package management instead.

## Conclusion
Through this lab, students will develop essential skills in system programming by writing and executing C programs using the vi editor, creating and linking static libraries, and exploring advanced Linux commands such as redirection and piping. These activities will serve as a foundation for understanding program compilation, efficient code reuse through libraries, and Linux system-level operations. This lab will prepare students for shell scripts writing, advanced topics in system programming and efficient software development in projects.

---
*Central Asian University, Tashkent SP25 System Programming*