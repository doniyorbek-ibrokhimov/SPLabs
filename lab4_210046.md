# LAB 4: Using Lists, Functions and Various Important Shell Scripting Commands

**Department of Computer Science, School of Engineering**  
**Report Submission Date:** Due in Next Lab

## Objectives:
The objective of this lab is to enhance understanding and proficiency in shell scripting by mastering the use of lists, functions, and various shell commands. Students will learn to manipulate data, use control structures for decision making, and handle file operations efficiently.

## Instructions:
• Utilize the lab effectively; avoid using cell phones.
• Use your personal systems for more flexibility.
• Must have Ubuntu; otherwise, use macOS and search for alternative commands and utilities.
• Refrain from just copying and pasting from ChatGPT; however, it is strongly encouraged to understand and execute variations available online.
• Create a Lab4 directory and write all scripts in that directory. Make a habit of creating a new directory for every lab.
• Test the below commands and scripts first. Also, execute all the commands from this week's lectures.

## Tasks

### Task 1: Checking Even/Odd and Calculating Factorial
Write a shell script where:
• A function accepts two numbers as arguments.
• The function checks whether the first number is even or odd and prints the result.
• The function calculates the factorial of the second number and prints the result.
• Call the function in the script and display the results.

**Solution:**

**`SP/labs/Lab4/task1.sh`**
```bash
#!/bin/bash

task1_function() {
    num1=$1
    num2=$2

    if (( num1 % 2 == 0 )); then
        echo "$num1 is Even"
    else
        echo "$num1 is Odd"
    fi

    factorial=1
    if (( num2 < 0 )); then
        echo "Factorial is not defined for negative numbers."
    elif (( num2 == 0 )); then
        echo "Factorial of 0 is 1"
    else
        for (( i=1; i<=num2; i++ )); do
            factorial=$((factorial * i))
        done
        echo "Factorial of $num2 is $factorial"
    fi
}

task1_function 3 5
echo ""
task1_function 4 0
echo ""
task1_function 7 -2
```

**Output:**
```text
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab4
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task1.sh
3 is Odd
Factorial of 5 is 120

4 is Even
Factorial of 0 is 1

7 is Odd
Factorial is not defined for negative numbers.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 %
```

### Task 2: Using AND and OR Lists
• Write a shell script that demonstrates the use of `&&` (AND list) and `||` (OR list).
  - Implement the following conditions:
    * If a file does not exist, create it using `touch`.
    * Use `cat` with redirection to input some content into the file, including the word "hello" in any line.
    * Check if the file exists, and then use `grep` to find the word "hello".
  - Ensure proper conditional execution using `&&` and `||`.

**Solution:**

**`SP/labs/Lab4/task2.sh`**
```bash
#!/bin/bash

FILE_NAME="testfile.txt"

# Create file if it doesn't exist
[ ! -f "$FILE_NAME" ] && touch "$FILE_NAME" && echo "File '$FILE_NAME' created."

# Write content to the file
cat << EOF > "$FILE_NAME"
This is a test file.
It contains the word hello.
Another line for testing.
EOF

echo "Content written to '$FILE_NAME'."

# Check for file and grep for "hello"
[ -f "$FILE_NAME" ] && { echo "Searching for 'hello' in '$FILE_NAME':"; grep "hello" "$FILE_NAME"; } || echo "File '$FILE_NAME' does not exist or 'hello' not found."

# Remove the file
rm "$FILE_NAME" && echo "File '$FILE_NAME' removed." || echo "Failed to remove '$FILE_NAME'."

```

**Output:**
```text
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab4
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task2.sh
File 'testfile.txt' created.
Content written to 'testfile.txt'.
Searching for 'hello' in 'testfile.txt':
It contains the word hello.
File 'testfile.txt' removed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 %
```

### Task 3: Sum of Digits in a Number
Write a shell script program to find the sum of digits in a number using a function. Use `%` and `/` to extract and remove the last digit until reaching zero. Return the sum using `echo`.

**Solution:**

**`SP/labs/Lab4/task3.sh`**
```bash
#!/bin/bash

sum_digits() {
    local number=$1
    local sum=0
    local digit

    if [[ ! "$number" =~ ^[0-9]+$ ]]; then
        echo "Error: Please provide a non-negative integer."
        return 1
    fi

    while (( number > 0 )); do
        digit=$(( number % 10 ))
        sum=$(( sum + digit ))
        number=$(( number / 10 ))
    done
    echo $sum
}

echo -n "Enter a number: "
read num

result=$(sum_digits $num)

if [[ $? -eq 0 ]]; then
    echo "Sum of digits for $num is: $result"
fi
```

**Output:**
```text
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab4
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task3.sh
Enter a number: 123
Sum of digits for 123 is: 6
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task3.sh
Enter a number: 405
Sum of digits for 405 is: 9
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task3.sh
Enter a number: abc
Error: Please provide a non-negative integer.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 %
```

### Task 4: Finding the Greatest Number
Write a shell script that asks users to enter three numbers using a single `read` command. Pass these numbers to a function. The function should find and return the greatest number using `echo`.

**Solution:**

**`SP/labs/Lab4/task4.sh`**
```bash
#!/bin/bash

find_greatest() {
    local n1=$1
    local n2=$2
    local n3=$3

    if (( n1 >= n2 && n1 >= n3 )); then
        echo $n1
    elif (( n2 >= n1 && n2 >= n3 )); then
        echo $n2
    else
        echo $n3
    fi
}

echo -n "Enter three numbers separated by spaces: "
read num1 num2 num3

greatest=$(find_greatest $num1 $num2 $num3)
echo "The greatest number is: $greatest"
```

**Output:**
```text
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab4
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task4.sh
Enter three numbers separated by spaces: 10 20 5
The greatest number is: 20
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task4.sh
Enter three numbers separated by spaces: 30 15 25
The greatest number is: 30

### Task 5: File Comparison and Deletion
Using `cat` and output redirection, create a file with some delimiter. Redirect the contents to another file, ensuring both have identical content. Write a shell script that:
• Accepts two filenames as arguments.
• Checks if two arguments are given (`$#`). If not, exit with `exit 1`.
• Verifies if both files exist. If not, exit with `exit 1`.
• Use `cmp -s` to compare the files and removes the second file if they are identical.

**Solution:**

**`SP/labs/Lab4/task5.sh`**
```bash
#!/bin/bash

if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <file1> <file2>"
    exit 1
fi

FILE1="$1"
FILE2="$2"

if [ ! -f "$FILE1" ]; then
    echo "Error: File '$FILE1' does not exist."
    exit 1
fi

if [ ! -f "$FILE2" ]; then
    echo "Error: File '$FILE2' does not exist."
    exit 1
fi

if cmp -s "$FILE1" "$FILE2"; then
    echo "Files '$FILE1' and '$FILE2' are identical."
    rm "$FILE2"
    echo "File '$FILE2' has been removed."
else
    echo "Files '$FILE1' and '$FILE2' are different."
fi
```

**Output:**
```text
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 % cd SP/labs/Lab4

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % echo "Hello World\nThis is a test file." > fileA.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % cp fileA.txt fileB.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task5.sh fileA.txt fileB.txt
Files 'fileA.txt' and 'fileB.txt' are identical.
File 'fileB.txt' has been removed.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % ls fileB.txt
ls: fileB.txt: No such file or directory
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % rm fileA.txt

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % echo "Hello World\nThis is file C." > fileC.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % echo "Hello World\nThis is file D, it's different." > fileD.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task5.sh fileC.txt fileD.txt
Files 'fileC.txt' and 'fileD.txt' are different.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % ls fileD.txt
fileD.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % rm fileC.txt fileD.txt

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task5.sh onlyOneFile.txt
Usage: task5.sh <file1> <file2>

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % echo "Temp for test" > actualFile.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task5.sh nonExistent1.txt actualFile.txt
Error: File 'nonExistent1.txt' does not exist.

d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % bash task5.sh actualFile.txt nonExistent2.txt
Error: File 'nonExistent2.txt' does not exist.
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % rm actualFile.txt
d.ibrokhimov@Doniyorbeks-MacBook-Pro Lab4 % cd ../../..
d.ibrokhimov@Doniyorbeks-MacBook-Pro Spring25 %
```

## Example Commands and Scripts

### Echo Variations
```bash
echo -n "Hello, World!"
echo -e "Hello\nWorld!"
echo "hello" | tr -d "\n"
```

### Using printf
```bash
printf "Name: %s\nAge: %d\n" "Alice" 25
```

### Exit Status Handling
```bash
if [ $# -ne 2 ]; then
    echo "Usage: $0 arg1 arg2"
    exit 1
fi
```

### Using Return in a Function
```bash
function add() {
    return $(($1 + $2))
}
add 3 4
echo $?  # Outputs the sum
```

### Executing a Stored Command Using eval
```bash
cmd="ls -l"
eval $cmd
```

### Using exec to Replace the Current Shell Process
```bash
exec ls -l
```

### Arithmetic Calculations with expr
```bash
expr 5 + 3
```

### Setting and Shifting Variables
```bash
set apple banana cherry
echo "$1"  # Outputs: apple
shift
echo "$1"  # Outputs: banana
```

### Using trap to Handle Signals
```bash
trap "echo 'CTRL+C is disabled'" SIGINT
while true; do
    echo "Running... Press CTRL+Z to stop."
    sleep 2
done
```

### Using AND (&&) and OR (||) Lists
```bash
ls file.txt && echo "File exists"
ls missing.txt || echo "File not found"
```

### Exporting and Using Environment Variables
```bash
export MY_VAR="Hello World"
echo $MY_VAR
```

### Using eval to Dynamically Execute a Command
```bash
eval "echo This is dynamic"
```

### Using exec to Replace Shell Execution
```bash
exec echo "This will replace the current shell"
```

## Sending Email via Gmail SMTP with msmtp

Here's an example of how to send an email using Gmail's SMTP server with msmtp in a shell script:

```bash
#!/bin/bash
# sudo apt install msmtp

# Define variables
SMTP_SERVER="smtp.gmail.com"
SMTP_PORT="587"
USERNAME="your_email_id"
FROM="your_email_id"
PASSWORD="16_characters_code_for_2FA"
TO="recipient_email"
SUBJECT="Test Email"
MESSAGE="Hello, this is a test email from Linux using msmtp."

# Send email using msmtp
echo -e "Subject: $SUBJECT\n\n$MESSAGE" | msmtp \
    --host=$SMTP_SERVER --port=$SMTP_PORT --auth=on \
    --user=$USERNAME --passwordeval="echo $PASSWORD" \
    --tls=on --tls-starttls=on --from="$FROM" "$TO"

echo "Email sent successfully!"
```
### Important Notes for Gmail:
• Ensure you've installed msmtp with the command `sudo apt install msmtp` if on a Debian-based system.
• Replace the placeholder email addresses and password with your actual Gmail details. Use an App Password for 2FA if enabled.
• `--passwordeval="echo $PASSWORD"` is used here for demonstration; in practice, avoid hard-coding passwords. Consider using environment variables or secure configuration files.
• Be aware of Gmail's sending limits and usage policies to avoid account restrictions.

---
*Central Asian University, Tashkent SP25 System Programming*
