# Shell Scripting Assignments

> Based on Session 14 & Session 15 notes.
> Every script must start with `#!/bin/bash` and be pushed to the shell-practice repo.

---

## Assignment 1 — Hello World & Shebang

Write a script `01-hello.sh` that:

1. Prints `Hello, World!`
2. Prints the current date and time
3. Prints the name of the user running the script
4. Prints the directory the script is running from

**Expected output:**
```
Hello, World!
Today is: Fri May 23 10:30:00 IST 2026
Running as: ec2-user
Current directory: /home/ec2-user/scripts
```

---

## Assignment 2 — Variables & DRY Principle

Write a script `02-variables.sh` that:

1. Store your name, age, and city in variables
2. Print a sentence using all three variables
3. Calculate and print how many years until you turn 60
4. Change the name variable to a different name and verify only **one line** needs to change

**Expected output:**
```
My name is Siva, I am 28 years old and I live in Hyderabad.
Years until 60: 32
```

---

## Assignment 3 — Command Line Arguments

Write a script `03-args.sh` that accepts two arguments: a **username** and a **country**.

1. Print: `User <username> is from <country>`
2. Print total number of arguments passed
3. Print all arguments passed
4. Print the script name

Run it as: `sh 03-args.sh Siva India`

**Expected output:**
```
User Siva is from India
Number of arguments: 2
All arguments: Siva India
Script name: 03-args.sh
```

---

## Assignment 4 — Special Variables

Write a script `04-special-vars.sh` that demonstrates all special variables:

1. Print who is running the script
2. Print the current directory
3. Print the home directory
4. Print the PID of the script
5. Run `sleep 10` in the **background** and print its PID
6. Print a random number between 1–100
7. At the end, print how many seconds the script took to run

**Expected output:**
```
Running as     : ec2-user
Directory      : /home/ec2-user
Home           : /home/ec2-user
Script PID     : 12345
Background PID : 12346
Random number  : 73
Script ran in 0 seconds
```

---

## Assignment 5 — Exit Codes

Write a script `05-exit-code.sh` that:

1. Runs `ls /tmp` and checks if it succeeded — print `SUCCESS` or `FAILURE`
2. Runs `ls /fakedir` and checks — print `SUCCESS` or `FAILURE`
3. Runs `ping -c1 google.com` and checks — print `Host reachable` or `Host not reachable`
4. At the end, exit the script with code `0` if all commands succeeded, or `1` if any failed

* **SCRIPT:**
```
#!/bin/bash

# Initialize a variable to track if any command fails (0 means all good)
ANY_FAILED=0

# Validation Function
# Arguments: 
#   $1 - The exit status of the command ($?)
#   $2 - Message to print on SUCCESS
#   $3 - Message to print on FAILURE
validate() {
    local exit_status=$1
    local success_msg=$2
    local failure_msg=$3

    if [ $exit_status -eq 0 ]; then
        echo "$success_msg"
    else
        echo "$failure_msg"
        ANY_FAILED=1  # Mark that at least one error occurred
    fi
}

# 1. Run ls /tmp
ls /tmp &> /dev/null
validate $? "SUCCESS" "FAILURE"

# 2. Run ls /fakedir
ls /fakedir &> /dev/null
validate $? "SUCCESS" "FAILURE"

# 3. Run ping -c1 google.com
ping -c1 google.com &> /dev/null
validate $? "Host reachable" "Host not reachable"

# Final Check: Exit with 0 if all succeeded, or 1 if any failed
if [ $ANY_FAILED -eq 0 ]; then
    exit 0
else
    exit 1
fi
```

---

## Assignment 6 — Conditions: Even or Odd

Write a script `06-even-odd.sh` that:

1. Accepts a number as a command line argument
2. Checks if the number is even or odd using the modulo operator
3. Prints the result

Run as: `sh 06-even-odd.sh 11`

**Expected output:**
```
11 is odd
```

---

## Assignment 7 — Conditions: Prime Number or Not

Write a script `07-prime.sh` that:

1. Accepts a number as a command line argument
2. Validates that the argument is provided — exit with an error if missing
3. Checks whether the number is a **prime number** or not
4. Prints the result

A prime number is a number greater than 1 that has no divisors other than 1 and itself.

Run as: `sh 07-prime.sh 17`

**Expected output:**
```
17 is a prime number
```

```
sh 07-prime.sh 12
12 is not a prime number
```

---

## Assignment 8 — Conditions: Day Check

Write a script `08-day-check.sh` that:

1. Gets today's day automatically
2. If it is **Saturday or Sunday**, print `Weekend! Enjoy your holiday.`
3. If it is **Monday**, print `Start of the week. Let's go!`
4. For any other day, print `Weekday. Go to school/work.`

---
```
#!/bin/bash
day=$(date +%A)

if [ $day == "Sunday" ] || [ $day == "Saturday" ] ; then
        echo "Weekend! Enjoy your holiday."
elif [ $day == "Monday" ]; then
        echo "Start of the! go to office"
else
        echo "go to scholl"
fi
```
## Assignment 9 — Conditions: Package Installer

Write a script `09-install.sh` that:

1. Checks if the script is run as **root** — if not, print an error and exit with code `1`
2. Accepts a package name as argument (e.g., `nginx`)
3. Checks if the package is **already installed** — if yes, print `nginx is already installed` and exit
4. If not installed, installs it using `dnf install -y`
5. Checks the exit status after install — prints `nginx installed successfully` or `nginx installation failed`

Run as: `sudo sh 09-install.sh nginx`

---
```
#!/bin/bash

validate(){
if [ $1 -ne 0 ]; then
        echo "$2 installation failed"
else
        echo "$2 success"
fi
}

for package in $@
do
        dpkg -s $package &> /dev/null
        if [ $? -ne 0 ]; then
                echo "installing $package"

                apt install $package -y &> /dev/null
                validate $? "$package"
        else
                echo "$package already installed"
        fi
done
```

## Assignment 10 — Functions

Write a script `10-functions.sh` that defines the following functions:

1. `print_header` — prints a separator line and a heading passed as argument
2. `check_status` — accepts an exit code and a message; prints `SUCCESS: <message>` or `FAILURE: <message>`
3. `install_package` — accepts a package name; checks if installed, installs if not, uses `check_status` to report

Call `install_package` for: `git`, `wget`, `curl`

**Expected output:**
```
==========================================
Installing packages
==========================================
git is already installed
SUCCESS: wget installed
FAILURE: curl installation failed
```
```
#!/bin/bash

validate(){
if [ $1 -ne 0 ]; then
        echo "$2 installation failed"
else
        echo "$2 success"
fi
}

for package in $@
do
        dpkg -s $package &> /dev/null
        if [ $? -ne 0 ]; then
                echo "installing $package"

                apt install $package -y &> /dev/null
                validate $? "$package"
        else
                echo "$package already installed"
        fi
done
```
---

## Assignment 11 — Redirections & Logging

Write a script `11-log.sh` that:

1. Creates a log file at `/tmp/script.log`
2. Redirects all **success output** to the log file
3. Redirects all **error output** to `/tmp/script-errors.log`
4. Runs three commands: `ls /tmp`, `ls /fakedir`, `date`
5. At the end, prints the contents of both log files

---

## Assignment 12 — Putting It All Together

Write a script `12-system-info.sh` that:

1. Accepts one argument: a **server name** (label only)
2. Validates that the argument is provided — exit with error if missing
3. Defines a function `section` that prints a heading separator
4. Collects and prints:
   - Script start time and PID
   - Server label (from argument)
   - Current user and home directory
   - OS information (`cat /etc/os-release | grep PRETTY`)
   - Disk usage (`df -h /`)
   - Memory usage (`free -h`)
   - Uptime
5. Logs everything to `/tmp/system-info.log`
6. Prints total script execution time at the end

**Expected output (abbreviated):**
```
========================================
System Info Report — webserver-01
========================================
Script PID    : 9821
Started at    : 2026-05-23 10:30:00
User          : ec2-user
OS            : Red Hat Enterprise Linux 9.x
Disk (/)      : 15G used of 50G
Memory        : 2.1G used of 4.0G
Uptime        : up 3 days, 4:12
========================================
Script completed in 2 seconds
```

---

# 🐧 Shell Scripting Practice Problems

## Problems

**NOTE:** You can find the supporting files in this repo

### 1. Largest Number from Arguments
Write a script that finds the **largest number** from a list of numbers provided as **command-line arguments**.  

Example:
```bash
./largest.sh 12 56 3 89 42
# Output: Largest number is 89
```
```
large_num="$1"

for num in $@
do
        if [ $num -gt $large_num ]; then
                large_num="$num"

        fi
done

echo "$large_num"
```
---

### 2. Fibonacci Series
Write a script that prints the **Fibonacci series** up to `N` terms.  

Example for `N=7`:

---

### 3. Factorial of a Number
Write a script that calculates the **factorial** of a given number.  

Example:
- Input: `5` → Output: `120`

---

### 4. Pyramid of Stars
Write a script that prints a **pyramid of stars** with 5 lines.

Example:
```
    *
   ***
  *****
 *******
*********
```

---

### 5. Palindrome Check
Write a script that checks if a given **string or number** is a palindrome.  

Examples:
- `madam` → Palindrome  
- `12321` → Palindrome  
- `hello` → Not a Palindrome  

---

### 6. Random Password Generator
Write a script that generates a **random password** of a specified length.  
- The password should contain uppercase, lowercase, digits, and special characters.  
- Input: password length.  
- Output: random password string.

---

### 7. Word Frequency Counter
Write a script that reads a file and prints the **top 5 most repeated words** in that file, along with the **number of times** each word appears.

---

### 8. File Existence & Permissions
Write a script that accepts a filename (or path) as a **command-line argument** and prints whether:  
- The file exists  
- It is **readable**  
- It is **writable**  
- Or it is **not present**  

**Extension**: If the argument is a **directory**, list all files inside it.

---

### 9. Count Lines, Words, and Characters
Write a script that accepts a filename and prints:  
- The **number of lines**  
- The **number of words**  
- The **number of characters**  

Handle edge cases:  
- Empty file  
- Non-existent file  
- Permission denied  

---

### 10. Filter Lines from a File
Write a script that reads a large CSV/log file **line by line** and filters all lines that contain the word `ERROR`.  
- Save those lines into a separate output file.  
- Include a **header line** in the output.

---

### 11. String Replacement in Files
Write a script that replaces all occurrences of a string in multiple files.

- Replace the string `FOO` with `BAR` in all `.conf` files in a given directory and its subdirectories.  
- Use `find` + `sed` (or `perl -pi`).
