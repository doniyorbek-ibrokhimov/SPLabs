# Central Asian University, Tashkent, Uzbekistan
## Department of Computer Science, School of Engineering
**Class:** CS1-22/CS2-22/CS3-22/CS4-22  
**Date:** February 23rd, 2025  
**Subject:** System Programming  
**Instructor:** Dr. M. Bilal Qureshi  
**Due Date:** March 3rd, 2025 (11:59 PM)  
**Max Points:** (10×10) = 100

**Name:** Doniyorbek Ibrokhimov **Student ID #** 210046

# Theory Assignment 1- Spring 2025

All questions carry equal points.

## Question 1:
Write a shell script that takes a filename as an argument and uses grep to search for a specific pattern within the file. Implement error handling using trap to catch any errors that may occur during execution.

### Solution:

I've created a shell script named `search_pattern.sh` that takes a filename and a pattern as arguments. The script checks if the file exists and is readable, then uses grep to search for the specified pattern. It also implements error handling using trap to catch interruptions.

```bash
#!/bin/bash

# Exit codes
E_NOFILE=65
E_NOPERM=66
E_BADPATTERN=67

trap 'echo "Error: Script execution interrupted."; exit 1' INT TERM
trap 'echo "Error: Script received termination signal."; exit 1' HUP

if [ $# -lt 2 ]; then
    echo "Usage: $0 filename pattern"
    exit 1
fi

filename=$1
pattern=$2

if [ ! -f "$filename" ]; then
    echo "Error: File '$filename' does not exist."
    exit $E_NOFILE
fi

if [ ! -r "$filename" ]; then
    echo "Error: No permission to read '$filename'."
    exit $E_NOPERM
fi

echo "Searching for pattern '$pattern' in file '$filename'..."
grep_result=$(grep "$pattern" "$filename" 2>&1)

if [ $? -ne 0 ]; then
    if [[ "$grep_result" == *"invalid"* ]] || [[ "$grep_result" == *"syntax error"* ]]; then
        echo "Error: Invalid pattern '$pattern'."
        exit $E_BADPATTERN
    fi
    
    echo "Pattern not found or error occurred: $grep_result"
    exit 1
else
    echo "Results:"
    echo "$grep_result"
fi

echo "Search completed successfully."
exit 0
```

### Testing the Script

1. Testing with missing arguments:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./search_pattern.sh
Usage: ./search_pattern.sh filename pattern
```

2. Testing with a non-existent file:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./search_pattern.sh nonexistent.txt "pattern"
Error: File 'nonexistent.txt' does not exist.
```

3. Testing with a valid file and pattern:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./search_pattern.sh sample.txt "pattern"
Searching for pattern 'pattern' in file 'sample.txt'...
Results:
The script should be able to find patterns in this file.
For example, it should be able to find the word "pattern" in this line.
Search completed successfully.
```

4. Testing with an invalid pattern:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./search_pattern.sh sample.txt "[a-z"
Searching for pattern '[a-z' in file 'sample.txt'...
Pattern not found or error occurred: grep: brackets ([ ]) not balanced
```

The script successfully demonstrates error handling with the trap command, which would catch interruptions like Ctrl+C during execution. It also handles invalid patterns, missing files, and provides appropriate error messages.

## Question 2:
Create a shell script that demonstrates the usage of set, unset, and shift commands. Your script should receive command-line arguments, set some variables, unset them, and then shift the arguments to demonstrate the change.

### Solution:

I've created a shell script named `variable_commands.sh` that demonstrates the usage of set, unset, and shift commands. The script works with command-line arguments, sets and unsets variables, and shifts positional parameters to show how these commands affect the script's environment.

```bash
#!/bin/bash

echo "Initial arguments received from command line:"
echo "Number of arguments: $#"
echo "All arguments: $@"
if [ $# -gt 0 ]; then
    echo "First argument: $1"
    if [ $# -gt 1 ]; then
        echo "Second argument: $2"
    fi
fi
echo

original_args=("$@")

echo "Setting new positional parameters using 'set'"
set apple banana cherry date
echo "After set: $@"
echo "Parameter 1: $1"
echo "Parameter 2: $2"
echo "Parameter 3: $3"
echo "Parameter 4: $4"
echo

echo "Creating user variables"
FRUIT1="mango"
FRUIT2="orange"
echo "FRUIT1 = $FRUIT1"
echo "FRUIT2 = $FRUIT2"
echo

echo "Unsetting FRUIT1 variable"
unset FRUIT1
echo "FRUIT1 = $FRUIT1 (should be empty after unset)"
echo "FRUIT2 = $FRUIT2 (should still have value)"
echo

echo "Demonstrating shift command"
echo "Before shift: $@"
echo "Parameter 1 before shift: $1"
shift
echo "After shift by 1: $@"
echo "Parameter 1 after shift: $1"
echo

echo "Shifting by 2 positions"
echo "Before shift 2: $@"
echo "Parameter 1 before shift: $1"
shift 2
echo "After shift 2: $@"
echo "Parameter 1 after shift: $1"
echo

echo "Demonstrating set with command substitution"
set $(ls -1 | head -3)
echo "After set with command output: $@"
echo "Parameter 1: $1"
echo "Parameter 2: $2"
echo "Parameter 3: $3"
echo

if [ ${#original_args[@]} -gt 0 ]; then
    echo "Restoring original command-line arguments using 'set'"
    set -- "${original_args[@]}"
    echo "Restored arguments: $@"
    echo
    
    echo "Demonstrating shift with original arguments"
    while [ $# -gt 0 ]; do
        echo "Current first argument: $1"
        shift
        echo "Remaining arguments after shift: $@"
        echo
    done
fi

exit 0
```

### Testing the Script

Testing with command-line arguments:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./variable_commands.sh arg1 arg2 arg3
Initial arguments received from command line:
Number of arguments: 3
All arguments: arg1 arg2 arg3
First argument: arg1
Second argument: arg2

Setting new positional parameters using 'set'
After set: apple banana cherry date
Parameter 1: apple
Parameter 2: banana
Parameter 3: cherry
Parameter 4: date

Creating user variables
FRUIT1 = mango
FRUIT2 = orange

Unsetting FRUIT1 variable
FRUIT1 =  (should be empty after unset)
FRUIT2 = orange (should still have value)

Demonstrating shift command
Before shift: apple banana cherry date
Parameter 1 before shift: apple
After shift by 1: banana cherry date
Parameter 1 after shift: banana

Shifting by 2 positions
Before shift 2: banana cherry date
Parameter 1 before shift: banana
After shift 2: date
Parameter 1 after shift: date

Demonstrating set with command substitution
After set with command output: large_file.txt sample.txt search_pattern.sh
Parameter 1: large_file.txt
Parameter 2: sample.txt
Parameter 3: search_pattern.sh

Restoring original command-line arguments using 'set'
Restored arguments: arg1 arg2 arg3

Demonstrating shift with original arguments
Current first argument: arg1
Remaining arguments after shift: arg2 arg3

Current first argument: arg2
Remaining arguments after shift: arg3

Current first argument: arg3
Remaining arguments after shift: 
```

This script demonstrates the key commands as follows:

1. **set command**: Used to set positional parameters directly by providing values as arguments. We also use it with command substitution to set parameters from command output and to restore original arguments.

2. **unset command**: Used to remove the variable FRUIT1, showing how the variable becomes empty after unsetting while other variables remain unchanged.

3. **shift command**: Used to shift positional parameters left. The script shows shifting by one position and by multiple positions, demonstrating how parameters are reordered after each shift.

The script successfully shows how to manipulate positional parameters and variables using these built-in shell commands.

## Question 3:
Write a shell script that uses a for loop to iterate over a list of filenames in a directory obtained using the find command. Display each filename found.

### Solution:

I've created a shell script named `list_files.sh` that uses the find command to locate files in a directory and a for loop to iterate through the results. The script accepts an optional directory path as an argument (defaults to current directory if none provided).

```bash
#!/bin/bash

if [ $# -eq 0 ]; then
    dir_path="."
else
    dir_path="$1"
fi

echo "Listing files in directory: $dir_path"
echo "---------------------------------------"

echo "Files found using find command:"

for file in $(find "$dir_path" -type f -not -path "*/\.*"); do
    echo "Found: $file"
done

echo "---------------------------------------"

echo "Using find with -exec option:"
find "$dir_path" -type f -not -path "*/\.*" -exec echo "Found via exec: {}" \;

echo "---------------------------------------"

file_count=$(find "$dir_path" -type f -not -path "*/\.*" | wc -l)
echo "Total number of files found: $file_count"

exit 0
```

### Testing the Script

1. Creating a test directory with some files:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % mkdir -p test_dir && touch test_dir/file1.txt test_dir/file2.log test_dir/script.sh test_dir/data.csv && mkdir -p test_dir/subdir && touch test_dir/subdir/nested_file.txt
```

2. Testing the script with the test directory as an argument:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./list_files.sh test_dir
Listing files in directory: test_dir
---------------------------------------
Files found using find command:
Found: test_dir/file1.txt
Found: test_dir/script.sh
Found: test_dir/subdir/nested_file.txt
Found: test_dir/data.csv
Found: test_dir/file2.log
---------------------------------------
Using find with -exec option:
Found via exec: test_dir/file1.txt
Found via exec: test_dir/script.sh
Found via exec: test_dir/subdir/nested_file.txt
Found via exec: test_dir/data.csv
Found via exec: test_dir/file2.log
---------------------------------------
Total number of files found:        5
```

3. Running the script without arguments (using current directory):
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./list_files.sh
Listing files in directory: .
---------------------------------------
Files found using find command:
Found: ./list_files.sh
Found: ./large_file.txt
Found: ./search_pattern.sh
Found: ./test_dir/file1.txt
Found: ./test_dir/script.sh
Found: ./test_dir/subdir/nested_file.txt
Found: ./test_dir/data.csv
Found: ./test_dir/file2.log
Found: ./variable_commands.sh
Found: ./sample.txt
---------------------------------------
Using find with -exec option:
Found via exec: ./list_files.sh
Found via exec: ./large_file.txt
Found via exec: ./search_pattern.sh
Found via exec: ./test_dir/file1.txt
Found via exec: ./test_dir/script.sh
Found via exec: ./test_dir/subdir/nested_file.txt
Found via exec: ./test_dir/data.csv
Found via exec: ./test_dir/file2.log
Found via exec: ./variable_commands.sh
Found via exec: ./sample.txt
---------------------------------------
Total number of files found:       10
```

The script demonstrates two ways to work with the results of the find command:
1. Using a for loop to iterate through files returned by find
2. Using find's -exec option to perform actions on each file

It also shows how to count the total number of files found using a command pipeline with wc.

## Question 4:
Develop a shell script that utilizes while loop and case statement to continuously prompt the user for input until they enter a specific keyword to exit. The script should display a menu with options for the user to choose from using echo and read.

### Solution:

I've created a shell script named `menu_interface.sh` that implements an interactive menu system. The script uses a while loop to continuously display the menu and prompt for user input, and a case statement to process different menu options. The user can exit the menu by typing the keyword "quit".

```bash
#!/bin/bash

echo "Welcome to the Interactive Menu System"
echo "======================================"

exit_keyword="quit"
continue=true

while $continue; do
    echo
    echo "Menu Options:"
    echo "1. Display date and time"
    echo "2. List files in current directory"
    echo "3. Show disk usage" 
    echo "4. Show system uptime"
    echo "5. Show logged in users"
    echo "Type '$exit_keyword' to exit"
    echo

    read -p "Enter your choice: " choice
    echo

    case $choice in
        1)
            echo "Current date and time:"
            date
            ;;
        2)
            echo "Files in current directory:"
            ls -la
            ;;
        3)
            echo "Disk usage:"
            df -h
            ;;
        4)
            echo "System uptime:"
            uptime
            ;;
        5)
            echo "Currently logged in users:"
            who
            ;;
        $exit_keyword)
            echo "Exiting the menu system. Goodbye!"
            continue=false
            ;;
        *)
            echo "Invalid option. Please try again."
            ;;
    esac
done

exit 0
```

### Testing the Script

Here's the output when testing the script with various menu options:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % echo -e "1\n2\n3\ninvalid\n5\nquit\n" | ./menu_interface.sh
Welcome to the Interactive Menu System
======================================

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Current date and time:
Mon May 19 02:40:12 +05 2025

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Files in current directory:
total 664
drwxr-xr-x@ 11 d.ibrokhimov  staff     352 May 19 02:39 .
drwxr-xr-x   5 d.ibrokhimov  staff     160 May 19 02:26 ..
-rwxr-xr-x@  1 d.ibrokhimov  staff    2356 May 19 02:37 advanced_find.sh
-rw-r--r--@  1 d.ibrokhimov  staff  308894 May 19 02:27 large_file.txt
-rwxr-xr-x@  1 d.ibrokhimov  staff     892 May 19 02:37 list_files.sh
-rwxr-xr-x@  1 d.ibrokhimov  staff    1431 May 19 02:37 list_files_with_filter.sh
-rwxr-xr-x@  1 d.ibrokhimov  staff    1134 May 19 02:40 menu_interface.sh
-rw-r--r--@  1 d.ibrokhimov  staff     413 May 19 02:29 sample.txt
-rwxr-xr-x@  1 d.ibrokhimov  staff    1237 May 19 02:29 search_pattern.sh
drwxr-xr-x@  7 d.ibrokhimov  staff     224 May 19 02:35 test_dir
-rwxr-xr-x@  1 d.ibrokhimov  staff    1998 May 19 02:33 variable_commands.sh

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Invalid option. Please try again.

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Currently logged in users:
d.ibrokhimov     console      Apr 29 14:41 

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Exiting the menu system. Goodbye!
```

Testing the system uptime option:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % echo -e "4\nquit\n" | ./menu_interface.sh
Welcome to the Interactive Menu System
======================================

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


System uptime:
 2:40  up 19 days, 12:03, 1 user, load averages: 6.74 4.53 3.78

Menu Options:
1. Display date and time
2. List files in current directory
3. Show disk usage
4. Show system uptime
5. Show logged in users
Type 'quit' to exit


Exiting the menu system. Goodbye!
```

The script demonstrates the key concepts required:

1. **While loop**: Used to continuously present the menu until a specific condition is met (user enters the exit keyword).
2. **Case statement**: Used to perform different actions based on the user's choice.
3. **User input with read**: Used to get the user's menu selection.
4. **Exit condition**: The script terminates when the user enters a specific keyword ("quit").

Each menu option executes a different system command, providing a useful and interactive interface for common system operations.

## Question 5:
Design a shell script that takes two numbers as positional parameters and calculates their sum, difference, product, and quotient using expr. Display the results with appropriate messages.

### Solution:

I've created a shell script named `calculate_operations.sh` that takes two numbers as positional parameters and performs basic arithmetic operations using the `expr` command. The script calculates the sum, difference, product, quotient, and remainder of the two numbers.

```bash
#!/bin/bash

if [ $# -ne 2 ]; then
    echo "Error: Two numbers are required."
    echo "Usage: $0 <number1> <number2>"
    exit 1
fi

num1=$1
num2=$2

echo "Performing arithmetic operations on $num1 and $num2"
echo "=================================================="

if ! echo "$num1" | grep -q "^[+-]*[0-9]*$"; then
    echo "Error: First parameter is not a valid number."
    exit 1
fi

if ! echo "$num2" | grep -q "^[+-]*[0-9]*$"; then
    echo "Error: Second parameter is not a valid number."
    exit 1
fi

sum=$(expr $num1 + $num2)
echo "Sum: $num1 + $num2 = $sum"

difference=$(expr $num1 - $num2)
echo "Difference: $num1 - $num2 = $difference"

product=$(expr $num1 \* $num2)
echo "Product: $num1 * $num2 = $product"

if [ $num2 -eq 0 ]; then
    echo "Cannot calculate quotient: Division by zero is not allowed."
else
    quotient=$(expr $num1 / $num2)
    echo "Quotient: $num1 / $num2 = $quotient"
    
    remainder=$(expr $num1 % $num2)
    echo "Remainder: $num1 % $num2 = $remainder"
fi

exit 0
```

### Testing the Script

1. Testing with positive numbers:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calculate_operations.sh 10 5
Performing arithmetic operations on 10 and 5
==================================================
Sum: 10 + 5 = 15
Difference: 10 - 5 = 5
Product: 10 * 5 = 50
Quotient: 10 / 5 = 2
Remainder: 10 % 5 = 0
```

2. Testing with negative numbers:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calculate_operations.sh -15 3
Performing arithmetic operations on -15 and 3
==================================================
Sum: -15 + 3 = -12
Difference: -15 - 3 = -18
Product: -15 * 3 = -45
Quotient: -15 / 3 = -5
Remainder: -15 % 3 = 0
```

3. Testing division by zero:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calculate_operations.sh 20 0
Performing arithmetic operations on 20 and 0
==================================================
Sum: 20 + 0 = 20
Difference: 20 - 0 = 20
Product: 20 * 0 = 0
Cannot calculate quotient: Division by zero is not allowed.
```

4. Testing with missing parameters:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calculate_operations.sh 15
Error: Two numbers are required.
Usage: ./calculate_operations.sh <number1> <number2>
```

5. Testing with invalid input:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calculate_operations.sh 10 abc
Performing arithmetic operations on 10 and abc
==================================================
Error: Second parameter is not a valid number.
```

The script successfully demonstrates the use of the `expr` command to perform arithmetic operations on two numbers provided as positional parameters. It also includes proper error handling for invalid inputs and division by zero.

## Question 6:
Create a shell script that checks if a file exists. If the file exists, it prints a message saying "File already exists." If the file does not exist, it uses the touch command to create the file and prints a message saying, "File created successfully." Utilize the AND list (&&) to execute the touch command only if the file doesn't exist.

Once the file is created use find command with any test and by any action.

### Solution:

I've created a shell script named `file_check.sh` that checks if a specified file exists. If the file exists, it displays a message indicating so. If the file doesn't exist, it uses the AND list (&&) operator to create the file using touch and then displays a success message. After file creation, it demonstrates various uses of the find command with different tests and actions.

```bash
#!/bin/bash

if [ $# -eq 0 ]; then
    echo "Error: No filename provided."
    echo "Usage: $0 <filename>"
    exit 1
fi

filename=$1

if [ -f "$filename" ]; then
    echo "File '$filename' already exists."
    echo "File details:"
    ls -l "$filename"
else
    [ ! -f "$filename" ] && touch "$filename" && echo "File '$filename' created successfully."
    
    echo "Verifying file creation:"
    ls -l "$filename"
    
    echo
    echo "Demonstrating find command with -type test and -exec action:"
    find . -type f -name "$filename" -exec echo "Found file: {}" \;
    
    echo
    echo "File permissions:"
    find . -type f -name "$filename" -exec ls -l {} \;
    
    echo
    echo "Adding some content to the file:"
    echo "This file was created by file_check.sh script on $(date)" > "$filename"
    echo "Content added to the file."
    
    echo
    echo "Finding files modified in the last minute (includes our new file):"
    find . -type f -mmin -1
fi

exit 0
```

### Testing the Script

1. Testing with a new file to see the creation process and find command usage:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./file_check.sh test_file.txt
File 'test_file.txt' created successfully.
Verifying file creation:
-rw-r--r--@ 1 d.ibrokhimov  staff  0 May 19 02:45 test_file.txt

Demonstrating find command with -type test and -exec action:
Found file: ./test_file.txt

File permissions:
-rw-r--r--@ 1 d.ibrokhimov  staff  0 May 19 02:45 ./test_file.txt

Adding some content to the file:
Content added to the file.

Finding files modified in the last minute (includes our new file):
./test_file.txt
./file_check.sh
```

2. Testing with an existing file:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./file_check.sh test_file.txt
File 'test_file.txt' already exists.
File details:
-rw-r--r--@ 1 d.ibrokhimov  staff  78 May 19 02:45 test_file.txt
```

3. Testing with no arguments:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./file_check.sh
Error: No filename provided.
Usage: ./file_check.sh <filename>
```

4. Testing with another new file:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./file_check.sh another_test.txt
File 'another_test.txt' created successfully.
Verifying file creation:
-rw-r--r--@ 1 d.ibrokhimov  staff  0 May 19 02:45 another_test.txt

Demonstrating find command with -type test and -exec action:
Found file: ./another_test.txt

File permissions:
-rw-r--r--@ 1 d.ibrokhimov  staff  0 May 19 02:45 ./another_test.txt

Adding some content to the file:
Content added to the file.

Finding files modified in the last minute (includes our new file):
./another_test.txt
./test_file.txt
./file_check.sh
```

The script successfully demonstrates:
1. Using conditionals to check if a file exists with the `-f` test
2. Using the AND list (&&) operator to execute commands only if a condition is true
3. Using the touch command to create a new file
4. Using the find command with various tests (-type, -name, -mmin) and actions (-exec)

## Question 7:
Create a script that accepts a mathematical expression from the user and evaluates it using $(( )) and expr.

### Solution:

I've created a shell script named `calc_expression.sh` that accepts a mathematical expression either as a command-line argument or as user input. The script evaluates the expression using both the $(( )) syntax and the expr command, showing the results of both methods.

```bash
#!/bin/bash

echo "Mathematical Expression Evaluator"
echo "================================="

if [ $# -eq 1 ]; then
    expression="$1"
    echo "Expression provided as argument: $expression"
else
    read -p "Enter a mathematical expression (e.g., 5 + 3, 10 \* 2): " expression
fi

echo

echo "Evaluating with \$(( )) syntax:"
echo -n "Result: "
result_shell=$(( $expression ))
echo "$result_shell"

echo

echo "Evaluating with expr command:"
echo -n "Result: "

if [[ "$expression" == *"*"* ]]; then
    parts=(${expression//\*/ })
    if [ ${#parts[@]} -eq 2 ]; then
        num1=${parts[0]}
        num2=${parts[1]}
        result_expr=$(expr $num1 \* $num2 2>/dev/null || echo "Error: Invalid expression for expr")
        echo "$result_expr"
    else
        echo "Error: Complex multiplication not supported with expr"
    fi
elif [[ "$expression" == *"/"* ]]; then
    parts=(${expression//\// })
    if [ ${#parts[@]} -eq 2 ]; then
        num1=${parts[0]}
        num2=${parts[1]}
        if [ "$num2" -eq 0 ]; then
            echo "Error: Division by zero"
        else
            result_expr=$(expr $num1 / $num2 2>/dev/null || echo "Error: Invalid expression for expr")
            echo "$result_expr"
        fi
    else
        echo "Error: Complex division not supported with expr"
    fi
else
    result_expr=$(expr $expression 2>/dev/null || echo "Error: Invalid expression for expr")
    echo "$result_expr"
fi

echo 

echo "Both methods should produce the same result if the expression is valid."
echo "Note that $(( )) allows more complex operations and has different syntax rules than expr."

exit 0
```

### Testing the Script

1. Testing with addition:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "5 + 7"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 5 + 7

Evaluating with $(( )) syntax:
Result: 12

Evaluating with expr command:
Result: 12

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

2. Testing with subtraction:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "8 - 3"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 8 - 3

Evaluating with $(( )) syntax:
Result: 5

Evaluating with expr command:
Result: 5

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

3. Testing with multiplication:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "10 * 3"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 10 * 3

Evaluating with $(( )) syntax:
Result: 30

Evaluating with expr command:
Result: 30

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

4. Testing with division:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "15 / 3"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 15 / 3

Evaluating with $(( )) syntax:
Result: 5

Evaluating with expr command:
Result: 5

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

5. Testing division by zero:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "10 / 0"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 10 / 0

Evaluating with $(( )) syntax:
Result: ./calc_expression.sh: line 17: 10 / 0 : division by 0 (error token is " ")


Evaluating with expr command:
Result: Error: Division by zero

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

6. Testing with a complex expression:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./calc_expression.sh "5 + 3 * 2"
Mathematical Expression Evaluator
=================================
Expression provided as argument: 5 + 3 * 2

Evaluating with $(( )) syntax:
Result: 11

Evaluating with expr command:
Result: Error: Complex multiplication not supported with expr

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

7. Testing with user input:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % echo "20 - 7" | ./calc_expression.sh
Mathematical Expression Evaluator
=================================

Evaluating with $(( )) syntax:
Result: 13

Evaluating with expr command:
Result: 13

Both methods should produce the same result if the expression is valid.
Note that 0 allows more complex operations and has different syntax rules than expr.
```

The script demonstrates the differences between the $(( )) syntax and the expr command:

1. The $(( )) syntax is more versatile and can handle complex expressions with operator precedence.
2. The expr command requires special handling for certain operators like * (multiplication) which needs to be escaped.
3. Both methods can perform basic arithmetic operations (addition, subtraction, multiplication, division).
4. Both methods handle error conditions like division by zero, though they report errors differently.

## Question 8:
Implement a script that creates a backup of all .txt files in a directory, appending a timestamp to the backup name.

### Solution:

I've created a shell script named `backup_txt_files.sh` that creates backups of all .txt files in a specified directory (or the current directory if none is provided). The script appends a timestamp to each backup filename and stores all backups in a dedicated backup directory.

```bash
#!/bin/bash

echo "Backup Script for .txt Files"
echo "==========================="

backup_dir="backup_$(date +%Y%m%d)"
timestamp=$(date +%Y%m%d_%H%M%S)
source_dir="."

if [ $# -eq 1 ]; then
    source_dir="$1"
    echo "Source directory: $source_dir"
fi

if [ ! -d "$source_dir" ]; then
    echo "Error: Source directory '$source_dir' does not exist."
    exit 1
fi

echo "Creating backup directory: $backup_dir"
mkdir -p "$backup_dir"

echo "Scanning for .txt files in $source_dir..."
txt_files=$(find "$source_dir" -maxdepth 1 -name "*.txt" -type f)
txt_count=$(echo "$txt_files" | grep -c "^")

if [ $txt_count -eq 0 ]; then
    echo "No .txt files found in $source_dir."
    exit 0
fi

echo "Found $txt_count .txt files"
echo "Starting backup process..."

successful_backups=0

for file in $txt_files; do
    filename=$(basename "$file")
    backup_name="${filename%.*}_${timestamp}.${filename##*.}"
    
    echo "Backing up: $filename -> $backup_name"
    cp "$file" "$backup_dir/$backup_name"
    
    if [ $? -eq 0 ]; then
        successful_backups=$((successful_backups + 1))
        echo "  Backup successful: $backup_dir/$backup_name"
    else
        echo "  Failed to backup $filename"
    fi
done

echo
echo "Backup process completed."
echo "$successful_backups out of $txt_count files backed up successfully."
echo "Backup location: $backup_dir"

exit 0
```

### Testing the Script

1. Running the script with default current directory parameter:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./backup_txt_files.sh
Backup Script for .txt Files
===========================
Creating backup directory: backup_20250519
Scanning for .txt files in ....
Found 7 .txt files
Starting backup process...
Backing up: test1.txt -> test1_20250519_025247.txt
  Backup successful: backup_20250519/test1_20250519_025247.txt
Backing up: another_test.txt -> another_test_20250519_025247.txt
  Backup successful: backup_20250519/another_test_20250519_025247.txt
Backing up: test2.txt -> test2_20250519_025247.txt
  Backup successful: backup_20250519/test2_20250519_025247.txt
Backing up: large_file.txt -> large_file_20250519_025247.txt
  Backup successful: backup_20250519/large_file_20250519_025247.txt
Backing up: important_data.txt -> important_data_20250519_025247.txt
  Backup successful: backup_20250519/important_data_20250519_025247.txt
Backing up: test_file.txt -> test_file_20250519_025247.txt
  Backup successful: backup_20250519/test_file_20250519_025247.txt
Backing up: sample.txt -> sample_20250519_025247.txt
  Backup successful: backup_20250519/sample_20250519_025247.txt

Backup process completed.
7 out of 7 files backed up successfully.
Backup location: backup_20250519
```

2. Verifying the backup files were created correctly:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ls -l backup_20250519
total 656
-rw-r--r--@ 1 d.ibrokhimov  staff      78 May 19 02:52 another_test_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff      34 May 19 02:52 important_data_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff  308894 May 19 02:52 large_file_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff     413 May 19 02:52 sample_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff      20 May 19 02:52 test1_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff      20 May 19 02:52 test2_20250519_025247.txt
-rw-r--r--@ 1 d.ibrokhimov  staff      78 May 19 02:52 test_file_20250519_025247.txt
```

3. Running the script with a specific directory:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./backup_txt_files.sh test_dir
Backup Script for .txt Files
===========================
Source directory: test_dir
Creating backup directory: backup_20250519
Scanning for .txt files in test_dir...
Found 2 .txt files
Starting backup process...
Backing up: file1.txt -> file1_20250519_025307.txt
  Backup successful: backup_20250519/file1_20250519_025307.txt
Backing up: test_in_dir.txt -> test_in_dir_20250519_025307.txt
  Backup successful: backup_20250519/test_in_dir_20250519_025307.txt

Backup process completed.
2 out of 2 files backed up successfully.
Backup location: backup_20250519
```

4. Testing error handling with a non-existent directory:
```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./backup_txt_files.sh non_existent_dir
Backup Script for .txt Files
===========================
Source directory: non_existent_dir
Error: Source directory 'non_existent_dir' does not exist.
```

The script successfully demonstrates:

1. File backup functionality with timestamp appended to filenames
2. Directory creation for backup storage
3. Handling different scenarios:
   - Backing up files from the current directory
   - Backing up files from a specified directory
   - Handling errors for non-existent directories
   - Reporting success/failure statistics

The timestamp ensures that each backup is uniquely identified, making it easy to track when the backup was created and preventing overwriting of previous backups.

## Question 9:
Create a script that continuously monitors a given directory and notifies when a new file is added.

### Solution:

I've created a shell script named `monitor_directory.sh` that continuously monitors a specified directory and reports when new files are added. The script uses the `find` command to track files, and the `comm` utility to compare the current file list with the previous one to identify new files.

```bash
#!/bin/bash

echo "Directory Monitoring Script"
echo "=========================="

if [ $# -eq 0 ]; then
    monitor_dir="."
    echo "No directory specified, monitoring current directory."
else
    monitor_dir="$1"
    if [ ! -d "$monitor_dir" ]; then
        echo "Error: Directory '$monitor_dir' does not exist."
        exit 1
    fi
fi

echo "Starting to monitor directory: $monitor_dir"
echo "Press Ctrl+C to stop monitoring."
echo

temp_dir="$monitor_dir/monitored_dir"
mkdir -p "$temp_dir" 2>/dev/null

find_cmd="find \"$monitor_dir\" -maxdepth 1 -type f -not -path \"$monitor_dir/.*\" | sort"
initial_files=$(eval $find_cmd)

echo "Initial files in directory:"
if [ -z "$initial_files" ]; then
    echo "  No files found."
else
    echo "$initial_files" | while read -r file; do
        echo "  $(basename "$file")"
    done
fi

echo
echo "Monitoring started at $(date)"
echo "Waiting for new files..."
echo

trap "echo -e '\nMonitoring stopped.'; exit" INT TERM EXIT

while true; do
    sleep 2
    
    current_files=$(eval $find_cmd)
    
    new_files=$(comm -13 <(echo "$initial_files") <(echo "$current_files"))
    
    if [ -n "$new_files" ]; then
        echo "New file(s) detected at $(date):"
        echo "$new_files" | while read -r file; do
            file_basename=$(basename "$file")
            file_size=$(du -h "$file" | cut -f1)
            echo "  Added: $file_basename (Size: $file_size)"
        done
        echo
        initial_files="$current_files"
    fi
done
```

### Testing the Script

To thoroughly test the monitoring script, I created a test script that automatically adds various types of files to a test directory while the monitor script is running. The test demonstrates the script's ability to detect new files as they are added.

Here's the output from the test:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./test_monitor_script.sh
Starting monitor script...
Adding first file...
Adding second file with content...
Adding binary file...
Adding executable script...
Stopping monitor script...
============ Monitor Script Output ============
Directory Monitoring Script
==========================
Starting to monitor directory: test_monitor
Press Ctrl+C to stop monitoring.

Initial files in directory:
  No files found.

Monitoring started at Mon May 19 02:57:10 +05 2025
Waiting for new files...

New file(s) detected at Mon May 19 02:57:14 +05 2025:
  Added: file1.txt (Size:   0B)

New file(s) detected at Mon May 19 02:57:16 +05 2025:
  Added: document.txt (Size: 4.0K)

New file(s) detected at Mon May 19 02:57:20 +05 2025:
  Added: binary.dat (Size:  12K)

New file(s) detected at Mon May 19 02:57:22 +05 2025:
  Added: script.sh (Size: 4.0K)


Monitoring stopped.

Monitoring stopped.
============ Test Complete ============
```

The script successfully demonstrates:

1. Setting up a directory monitoring process
2. Detecting changes in real-time as files are added
3. Displaying information about the new files (name and size)
4. Gracefully handling the termination of the monitoring process

This monitoring script would be particularly useful for automation tasks, security monitoring, or backup processes where you need to be notified about new files appearing in a specific directory.

## Question 10:
Write a script that extracts usernames from the /etc/passwd file and prints them in sorted order.

### Solution:

I've created a shell script named `extract_usernames.sh` that extracts usernames from the /etc/passwd file, filters out comment lines, sorts them alphabetically, and displays the total count of users.

```bash
#!/bin/bash

echo "Username Extractor"
echo "================="

passwd_file="/etc/passwd"

if [ ! -f "$passwd_file" ]; then
    echo "Error: $passwd_file does not exist or is not accessible."
    exit 1
fi

if [ ! -r "$passwd_file" ]; then
    echo "Error: No permission to read $passwd_file."
    exit 1
fi

echo "Extracting usernames from $passwd_file..."
echo

echo "Usernames in alphabetical order:"
cut -d: -f1 "$passwd_file" | grep -v "^#" | sort

echo
echo "Total number of users: $(cut -d: -f1 "$passwd_file" | grep -v "^#" | wc -l)"

exit 0
```

### Testing the Script

I ran the script on my macOS system to extract and sort usernames from the /etc/passwd file:

```
d.ibrokhimov@Doniyorbeks-MacBook-Pro assignment1_scripts % ./extract_usernames.sh
Username Extractor
=================
Extracting usernames from /etc/passwd...

Usernames in alphabetical order:
_accessoryupdater
_amavisd
_analyticsd
_aonsensed
_appinstalld
_appleevents
_applepay
_appowner
_appserver
_appstore
_ard
_assetcache
_astris
_atsserver
_audiomxd
_avbdeviced
_avphidbridge
_backgroundassets
_biome
_calendar
_captiveagent
_ces
_clamav
_cmiodalassistants
_coreaudiod
_coremediaiod
_coreml
_ctkd
_cvmsroot
_cvs
_cyrus
_darwindaemon
_datadetectors
_demod
_devdocs
_devicemgr
_diskimagesiod
_displaypolicyd
_distnote
_dovecot
_dovenull
_dpaudio
_driverkit
_eligibilityd
_eppc
_findmydevice
_fpsd
_ftp
_gamecontrollerd
_geod
_hidd
_iconservices
_installassistant
_installcoordinationd
_installer
_jabber
_kadmin_admin
_kadmin_changepw
_knowledgegraphd
_krb_anonymous
_krb_changepw
_krb_kadmin
_krb_kerberos
_krb_krbtgt
_krbfast
_krbtgt
_launchservicesd
_lda
_locationd
_logd
_lp
_mailman
_mbsetupuser
_mcxalr
_mdnsresponder
_mmaintenanced
_mobileasset
_mobilegestalthelper
_modelmanagerd
_mysql
_naturallanguaged
_nearbyd
_netbios
_netstatistics
_networkd
_neuralengine
_notification_proxy
_nsurlsessiond
_oahd
_ondemand
_postfix
_postgres
_qtss
_reportmemoryexception
_reportsystemmemory
_rmd
_sandbox
_screensaver
_scsd
_securityagent
_sntpd
_softwareupdate
_spotlight
_sshd
_svn
_swtransparencyd
_systemstatusd
_taskgated
_teamsserver
_terminusd
_timed
_timezone
_tokend
_trustd
_trustevaluationagent
_unknown
_update_sharing
_usbmuxd
_uucp
_warmd
_webauthserver
_windowserver
_www
_wwwproxy
_xserverdocs
daemon
nobody
root

Total number of users:      128
```

The script successfully extracts usernames from the /etc/passwd file and sorts them alphabetically. It works by:

1. Reading the /etc/passwd file using the `cut` command to extract the first field (username)
2. Using `grep -v "^#"` to filter out any comment lines that begin with #
3. Sorting the usernames alphabetically using the `sort` command
4. Counting and displaying the total number of users using `wc -l`

This demonstrates knowledge of text processing utilities like cut, grep, sort, and wc, as well as command pipelines to combine these utilities effectively.

## Instructions:
1. For each question, provide a clear explanation of what the script is expected to do, comments are necessary.
2. Demonstrate your scripts with appropriate sample inputs and outputs.
3. Ensure your scripts adhere to best practices of shell scripting, including error handling and comments.
4. Submit your answers along with the script code + output in a single pdf document, jpgs are not acceptable.
5. No late assignment will be entertained.
6. On 26th February 2025 you can expect your 1st Quiz from chapter # 2 of the text book.