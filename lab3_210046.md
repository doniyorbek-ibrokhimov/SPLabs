# LAB 3: Getting Started with Shell Scripting
## Use of Variables, Environment and Parameters, Conditional and Control Structures, Writing and Running Scripts

**Muhammad Bilal Qureshi**  
Department of Computer Science, School of Engineering  
**Report Submission Date:** Due in Next Lab

---

## Instructions
• Utilize the lab effectively; avoid using cell phones.
• Use your personal systems for more flexibility.
• Must have Ubuntu; otherwise, use macOS and search for alternative commands and utilities.
• Refrain from just copying and pasting from ChatGPT, however, strongly encouraged to understand and execute variations available online.
• Create a Lab3 directory and write all scripts in that directory. Make a habit of creating a new directory for every lab.

---

## Objectives
This lab introduces students to shell scripting fundamentals, focusing on:
• Using shell variables and environment variables.
• Understanding parameters and how to pass arguments to scripts.
• Implementing conditional statements and control structures.
• Writing scripts to automate common system tasks.
• Running and debugging scripts effectively.

---

## Tasks

### Task 1: Working with Variables and Parameters and Some Basics
• Write a script to count the number of characters in a string that is entered by the user using `wc -m`.  
  **Hint:** Use `read` to take input and `echo -n` to avoid counting newline characters.

• Write a script that captures and displays system environment variables. The script should also accept user input as parameters and display the values accordingly from the entered environment variable.  
  **Hint:** Use `printenv` for a complete comprehensive list and use a variable like this to access the contents `${!var_name}`.

**Solution for Task 1.1 (Character Count):**

**Script (`SP/labs/Lab3/count_chars.sh`):**
```bash
#!/bin/bash
echo -n "Enter a string: "
read user_string
echo -n "$user_string" | wc -m
```

**Execution and Output:**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x count_chars.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ printf "Hello World\n" | ./count_chars.sh
Enter a string:       11
```

**Solution for Task 1.2 (Environment Variables):**

**Script (`SP/labs/Lab3/show_env.sh`):**
```bash
#!/bin/bash
echo "System Environment Variables:"
printenv
echo
if [ -n "$1" ]; then
    echo "Value of environment variable '$1':"
    # Using indirect expansion as hinted: ${!var_name}
    echo "${!1}"
else
    echo "No environment variable name provided as a parameter."
fi
```

**Execution and Output (with parameter `USER`):**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x show_env.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./show_env.sh USER
System Environment Variables:
TERM_PROGRAM=vscode
TERM=xterm-256color
SHELL=/bin/zsh
HOMEBREW_REPOSITORY=/opt/homebrew
TMPDIR=/var/folders/09/8kx5kjs57r1bg4p_j60n8y6r0000gn/T/
USER=d.ibrokhimov
PATH=/opt/homebrew/opt/libpq/bin:/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/local/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/appleinternal/bin:/Library/Apple/usr/bin:/opt/homebrew/opt/libpq/bin
PWD=/Users/d.ibrokhimov/Documents/Spring25
LANG=en_US.UTF-8
HOME=/Users/d.ibrokhimov
LOGNAME=d.ibrokhimov
# ... (other environment variables displayed by printenv would appear here)

Value of environment variable 'USER':
d.ibrokhimov
```

### Task 2: Implementing Conditional Statements
• Write a script to determine if the year entered by the user is a leap year using a nested if-else control structure.  
  **Hint:** Check divisibility by 4 and 100 using the modulus operator.

• Create a script that checks for system resource usage (CPU or memory). If usage crosses a defined threshold, display a warning message.  
  **Hint:** Use `top -l 1` for macOS or `free -m` for Linux memory usage.

**Solution for Task 2.1 (Leap Year):**

**Script (`SP/labs/Lab3/leap_year.sh`):**
```bash
#!/bin/bash
echo -n "Enter a year: "
read year
if [ $((year % 4)) -eq 0 ]; then
  if [ $((year % 100)) -eq 0 ]; then
    if [ $((year % 400)) -eq 0 ]; then
      echo "$year is a leap year."
    else
      echo "$year is not a leap year."
    fi
  else
    echo "$year is a leap year."
  fi
else
  echo "$year is not a leap year."
fi
```

**Execution and Output:**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x leap_year.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ printf "2024\n" | ./leap_year.sh
Enter a year: 2024 is a leap year.
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ printf "2023\n" | ./leap_year.sh
Enter a year: 2023 is not a leap year.
```

**Solution for Task 2.2 (Resource Check - CPU on macOS):**

**Script (`SP/labs/Lab3/resource_check.sh`):**
```bash
#!/bin/bash
CPU_THRESHOLD=80
echo "Checking CPU Usage..."
CPU_STATS=$(top -l 1 | grep 'CPU usage:')
USER_CPU=$(echo "$CPU_STATS" | awk '{print $3}' | sed 's/%//')
SYS_CPU=$(echo "$CPU_STATS" | awk '{print $5}' | sed 's/%//')
IDLE_CPU=$(echo "$CPU_STATS" | awk '{print $7}' | sed 's/%//')
TOTAL_CPU_USAGE=$(echo "$USER_CPU + $SYS_CPU" | bc)
echo "Current CPU Usage: User $USER_CPU%, System $SYS_CPU%, Idle $IDLE_CPU%"
echo "Total active CPU Usage: $TOTAL_CPU_USAGE%"
if (( $(echo "$TOTAL_CPU_USAGE > $CPU_THRESHOLD" | bc -l) )); then
    echo "WARNING: CPU usage ($TOTAL_CPU_USAGE%) is above the threshold of $CPU_THRESHOLD%."
else
    echo "CPU usage ($TOTAL_CPU_USAGE%) is within normal limits (Threshold: $CPU_THRESHOLD%)."
fi
```

**Execution and Output:**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x resource_check.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./resource_check.sh
Checking CPU Usage...
Current CPU Usage: User 16.72%, System 32.44%, Idle 50.83%
Total active CPU Usage: 49.16%
CPU usage (49.16%) is within normal limits (Threshold: 80%).
```

### Task 3: Using Loops (For, While, Until)
• Write a shell script program using a while loop to calculate the factorial of a number entered by the user.  
  **Hint:** Initialize a variable to 1 and multiply it inside the loop.

• Write a script that monitors active users on the system in real time using a loop. It should continuously check and display logged-in users until the user terminates the script.  
  **Hint:** Use `who` to display currently logged-in users.

**Solution for Task 3.1 (Factorial Calculation):**

**Script (`SP/labs/Lab3/factorial.sh`):**
```bash
#!/bin/bash
echo -n "Enter a number: "
read num
if ! [[ "$num" =~ ^[0-9]+$ ]] || [ "$num" -lt 0 ]; then
    echo "Error: Please enter a non-negative integer."
    exit 1
fi
factorial=1
i=1
while [ "$i" -le "$num" ]; do
    factorial=$((factorial * i))
    i=$((i + 1))
done
echo "Factorial of $num is $factorial"
```

**Execution and Output (input `5`):**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x factorial.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ printf "5\n" | ./factorial.sh
Enter a number: Factorial of 5 is 120
```

**Solution for Task 3.2 (Monitor Active Users):**

**Script (`SP/labs/Lab3/monitor_users.sh`):**
```bash
#!/bin/bash
echo "Monitoring active users. Press Ctrl+C to stop."
while true; do
    echo "=== Active Users on $(date) ==="
    who
    sleep 5
done
```

**Example Execution and Output (output shows a couple of iterations):**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x monitor_users.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./monitor_users.sh
Monitoring active users. Press Ctrl+C to stop.
=== Active Users on Tue Jul 23 10:30:00 UTC 2024 ===
d.ibrokhimov console      2024-07-23 10:00 (:0)
d.ibrokhimov pts/0        2024-07-23 10:05 (192.168.1.10)
=== Active Users on Tue Jul 23 10:30:05 UTC 2024 ===
d.ibrokhimov console      2024-07-23 10:00 (:0)
d.ibrokhimov pts/0        2024-07-23 10:05 (192.168.1.10)
(Press Ctrl+C to terminate)
^C
```

### Task 4: Case Structure for System Tasks
• Write a script that displays a menu using a while loop and a case statement repeatedly. Implement commands for: `tail`, `cmp`, `uniq`, and `rm -r`.  
  **Hint:** Use a loop to continuously show menu options.

• Develop a script that allows the user to select an administrative task (e.g., checking disk space, monitoring network status, listing processes) using a case statement.  
  **Hint:** Use `df -h`, `top`, or `ps aux` for system monitoring.

**Solution for Task 4.1 (Menu Script):**

**Supporting files created in `SP/labs/Lab3/`:**
- `sample_file1.txt` (6 lines of text)
- `sample_file2.txt` (identical to `sample_file1.txt`)
- `sample_file3.txt` (different from `sample_file1.txt`)
- `uniq_sample.txt` (text with duplicate lines)
- `dummy_dir_for_rm/test_in_dummy.txt` (a directory with a file for `rm -r` testing)

**Script (`SP/labs/Lab3/menu_script.sh`):**
```bash
#!/bin/bash

while true; do
    echo "\nMenu:"
    echo "1. tail a file"
    echo "2. compare two files (cmp)"
    echo "3. show unique lines in a file (uniq)"
    echo "4. remove a directory (rm -r ./dummy_dir_for_rm)"
    echo "5. Exit"
    echo -n "Enter your choice: "
    read choice

    case $choice in
        1)
            echo -n "Enter filename: "
            read filename
            echo -n "Enter number of lines for tail: "
            read num_lines
            if [ -f "$filename" ]; then
                tail -n "$num_lines" "$filename"
            else
                echo "Error: File '$filename' not found."
            fi
            ;;
        2)
            echo -n "Enter first filename: "
            read file1
            echo -n "Enter second filename: "
            read file2
            if [ ! -f "$file1" ]; then
                echo "Error: File '$file1' not found."
            elif [ ! -f "$file2" ]; then
                echo "Error: File '$file2' not found."
            else
                cmp "$file1" "$file2"
                if [ $? -eq 0 ]; then
                    echo "Files are identical."
                fi
            fi
            ;;
        3)
            echo -n "Enter filename for uniq: "
            read filename
            if [ -f "$filename" ]; then
                uniq "$filename"
            else
                echo "Error: File '$filename' not found."
            fi
            ;;
        4)
            TARGET_DIR="./dummy_dir_for_rm"
            if [ -d "$TARGET_DIR" ]; then
                echo "Attempting to remove $TARGET_DIR..."
                rm -r "$TARGET_DIR"
                if [ $? -eq 0 ]; then
                    echo "Directory $TARGET_DIR removed successfully."
                else
                    echo "Error removing directory $TARGET_DIR."
                fi
            else
                echo "Directory $TARGET_DIR does not exist or already removed."
            fi
            mkdir -p "$TARGET_DIR" && touch "$TARGET_DIR/test_in_dummy.txt"
            echo "(Dummy directory recreated for potential next run)"
            ;;
        5)
            echo "Exiting."
            break
            ;;
        *)
            echo "Invalid choice. Please try again."
            ;;
    esac
done
```

**Execution and Output (simulated interaction, commands run from `SP/labs/Lab3/`):**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x menu_script.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./menu_script.sh

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 1
Enter filename: sample_file1.txt
Enter number of lines for tail: 3
line4
line5
line6

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 2
Enter first filename: sample_file1.txt
Enter second filename: sample_file2.txt
Files are identical.

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 2
Enter first filename: sample_file1.txt
Enter second filename: sample_file3.txt
sample_file1.txt sample_file3.txt differ: char 11, line 2

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 3
Enter filename for uniq: uniq_sample.txt
apple
banana
cherry
date

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 4
Attempting to remove ./dummy_dir_for_rm...
Directory ./dummy_dir_for_rm removed successfully.
(Dummy directory recreated for potential next run)

Menu:
1. tail a file
2. compare two files (cmp)
3. show unique lines in a file (uniq)
4. remove a directory (rm -r ./dummy_dir_for_rm)
5. Exit
Enter your choice: 5
Exiting.
```

**Solution for Task 4.2 (Admin Tasks Script):**

**Script (`SP/labs/Lab3/admin_tasks.sh`):**
```bash
#!/bin/bash

while true; do
    echo "\nAdmin Tasks Menu:"
    echo "1. Check disk space (df -h)"
    echo "2. Show network interface statistics (netstat -i)"
    echo "3. List all running processes (ps aux)"
    echo "4. Exit"
    echo -n "Enter your choice: "
    read choice

    case $choice in
        1)
            echo "\n--- Disk Space (df -h) ---"
            df -h | cat
            ;;
        2)
            echo "\n--- Network Interface Statistics (netstat -i) ---"
            netstat -i | cat
            ;;
        3)
            echo "\n--- All Running Processes (ps aux) ---"
            ps aux | cat
            ;;
        4)
            echo "Exiting."
            break
            ;;
        *)
            echo "Invalid choice. Please try again."
            ;;
    esac
done
```

**Execution and Output (simulated interaction, output from commands is excerpted):**
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x admin_tasks.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./admin_tasks.sh

Admin Tasks Menu:
1. Check disk space (df -h)
2. Show network interface statistics (netstat -i)
3. List all running processes (ps aux)
4. Exit
Enter your choice: 1

--- Disk Space (df -h) ---
Filesystem      Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk3s1s1  460Gi   16Gi  230Gi     7%  570759 2411051001    0%   /
... (output truncated) ...

Admin Tasks Menu:
1. Check disk space (df -h)
2. Show network interface statistics (netstat -i)
3. List all running processes (ps aux)
4. Exit
Enter your choice: 2

--- Network Interface Statistics (netstat -i) ---
Name       Mtu   Network       Address            Ipkts Ierrs    Idrop       Opkts Oerrs     Coll
lo0   16384  <Link#1>                         6709161     0        0     6709161     0        0
... (output truncated) ...

Admin Tasks Menu:
1. Check disk space (df -h)
2. Show network interface statistics (netstat -i)
3. List all running processes (ps aux)
4. Exit
Enter your choice: 3

--- All Running Processes (ps aux) ---
USER               PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
d.ibrokhimov     33568   0.0  0.0 410199728   1184 s009  S+    8:10PM   0:00.00 cat
root               349   0.2  0.1 427038688   8624   ??  Ss   29Apr25  49:06.59 /usr/libexec/logd
... (output truncated) ...

Admin Tasks Menu:
1. Check disk space (df -h)
2. Show network interface statistics (netstat -i)
3. List all running processes (ps aux)
4. Exit
Enter your choice: 4
Exiting.
```

### Task 5: Writing and Running a Script with Logical Operations
• Design a script that periodically checks a given directory for file modifications and logs any changes. It should continue running until the user chooses to exit.  
  **Hint:** Use `inotifywait` for Linux or `fswatch` for macOS.

**Solution for Task 5.1 (File Modification Monitor):**

**Supporting directory created: `SP/labs/Lab3/monitored_dir`**

**Script (`SP/labs/Lab3/file_monitor.sh`):**
```bash
#!/bin/bash
MONITOR_DIR="./monitored_dir"
LOG_FILE="./file_changes.log"

if ! command -v fswatch &> /dev/null; then
    echo "Error: fswatch is not installed. Please install it to use this script."
    echo "On macOS, you can use: brew install fswatch"
    exit 1
fi

if [ ! -d "$MONITOR_DIR" ]; then
    echo "Error: Monitored directory '$MONITOR_DIR' does not exist."
    echo "Please create it first relative to the script location."
    exit 1
fi

echo "Monitoring directory '$MONITOR_DIR' for changes..."
echo "Log file: '$LOG_FILE'"
echo "Press Ctrl+C to stop."

touch "$LOG_FILE"

fswatch -r "$MONITOR_DIR" | while read -r change; do
    timestamp=$(date)
    echo "[$timestamp] Change detected: $change" >> "$LOG_FILE"
    echo "Logged change: $change"
done
```

**Example Execution and Log Output (commands run from `SP/labs/Lab3/`):**

Terminal 1 (running the monitor):
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ chmod +x file_monitor.sh
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ ./file_monitor.sh
Monitoring directory './monitored_dir' for changes...
Log file: './file_changes.log'
Press Ctrl+C to stop.
(Leave this running)
```

Terminal 2 (simulating a file change):
```text
d.ibrokhimov@user-host:~/Documents/Spring25/SP/labs/Lab3$ echo "new content" > monitored_dir/another_file.txt
```

Output in Terminal 1 after the change:
```text
Logged change: monitored_dir/another_file.txt IsFile EventSystem Removed Renamed
```
(Note: `fswatch` might show multiple events for one operation like create/write)

After pressing Ctrl+C in Terminal 1, the `SP/labs/Lab3/file_changes.log` would contain:
```text
[Timestamp of fswatch start or previous events]
[Tue Jul 23 10:50:00 UTC 2024] Change detected: monitored_dir/another_file.txt IsFile EventSystem Removed Renamed
...
```
(Actual timestamp and exact fswatch event details will vary)

---

## Important Note
It is necessary to understand and execute all scripts and commands discussed during Lecture 4 and 5.

---

## Comparison Operators in Shell Scripting

### String Comparison
| Operator | Result |
|----------|--------|
| `string1 = string2` | True if the strings are equal |
| `string1 != string2` | True if the strings are not equal |
| `-n string` | True if the string is not null |
| `-z string` | True if the string is null (an empty string) |

### Arithmetic Comparison
| Operator | Result |
|----------|--------|
| `expression1 -eq expression2` | True if the expressions are equal |
| `expression1 -ne expression2` | True if the expressions are not equal |
| `expression1 -gt expression2` | True if expression1 is greater than expression2 |
| `expression1 -ge expression2` | True if expression1 is greater than or equal to expression2 |
| `expression1 -lt expression2` | True if expression1 is less than expression2 |
| `expression1 -le expression2` | True if expression1 is less than or equal to expression2 |
| `! expression` | True if the expression is false, and vice versa |

### File Conditional
| Operator | Result |
|----------|--------|
| `-d file` | True if the file is a directory |
| `-e file` | True if the file exists, -e has not been portable, so -f is usually used |
| `-f file` | True if the file is a regular file |
| `-g file` | True if set-group-id is set on file |
| `-r file` | True if the file is readable |
| `-s file` | True if the file has nonzero size |
| `-u file` | True if set-user-id is set on file |
| `-w file` | True if the file is writable |
| `-x file` | True if the file is executable |

---

## Essential Linux Commands for System Administration

### Monitoring Active Users in Real Time

#### Shell Script: Monitor Active Users
```bash
#!/bin/bash
echo "Monitoring active users. Press Ctrl+C to stop."
while true; do
    echo "=== Active Users on $(date) ==="
    who
    sleep 5
done
```

#### Alternative Using 'w' Command
```bash
#!/bin/bash
echo "Monitoring active users with 'w' command. Press Ctrl+C to stop."
while true; do
    echo "=== Active Users on $(date) ==="
    w
    sleep 5
done
```

### Debugging Scripts

#### Run Script in Debug Mode
```bash
bash -x script.sh
```

## Essential System Administration Commands

### Creating a User in Ubuntu

#### Command to Create a User
```bash
sudo useradd username
```

#### Check if User Exists
```bash
cat /etc/passwd | grep username
```

### Install SSH Server

#### Install OpenSSH Server
```bash
sudo apt install -y openssh-server
```

#### Start and Enable SSH
```bash
sudo systemctl start ssh
sudo systemctl enable ssh
sudo systemctl status ssh
```

#### Allow SSH Through Firewall
```bash
sudo ufw allow ssh
sudo ufw enable
```

### Simple IP Lookup

#### Check System IP Address
```bash
ip a
```

### Check for Unprintable Characters in Variables (e.g., IFS)

#### Print Unprintable Characters in Variables
```bash
printf '%q\n' "$IFS"
```

### Monitor System Resource Usage

#### Check System Resource Usage
```bash
top -bn1
```

---
*Central Asian University, Tashkent SP25 System Programming*