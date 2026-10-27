# Python for Cybersecurity — Study Notes

Part 1: Python fundamentals  
Syntax, print(), comments, variables, data types, type conversion, input(), strings and operators.

Part 2: Conditional statements  
if, elif, else, comparison operators, logical operators, nested conditions and input validation.

Part 3: Cybersecurity applications  
Password-strength checks, suspicious login detection, HTTP status checks, port-number validation and log analysis examples.

Part 4: Practice and interview questions  
Concept questions, output prediction, coding exercises and cybersecurity scenarios with answers.

---

# Python for Cybersecurity — Complete Beginner Notes

## From Python Basics to Conditional Statements

**Target:** Beginner preparing for a cybersecurity career  
**Language:** Python 3  
**Focus:** Concepts, code examples, cybersecurity applications, interview questions and practice problems

---

# 1. Introduction to Python

Python is a high-level programming language used to automate tasks, process data, interact with APIs, and build security tools.

### How Python is used in cybersecurity

- **Security automation:** Automate repetitive security checks.
- **Log analysis:** Identify suspicious activity in system and application logs.
- **Network security:** Inspect network information and analyze connections.
- **Web security:** Send HTTP requests and examine responses.
- **Incident response:** Extract indicators such as IP addresses, timestamps and file hashes.
- **Penetration testing:** Build authorized testing scripts.
- **Malware analysis:** Examine suspicious files and their behavior in a controlled environment.

### Basic Python program

```python
print("Hello, Cybersecurity!")
```

Output:

```text
Hello, Cybersecurity!
```

Python executes instructions in order, from top to bottom, unless control-flow statements change the execution path.

---

# 2. Python Syntax and Indentation

Syntax means the rules used to write valid Python code.

```python
username = "admin"
print(username)
```

Python uses indentation (spaces at the beginning of a line) to define blocks of code.

```python
if username == "admin":
    print("Administrator account detected")
```

The indented statement belongs to the `if` block.

Incorrect indentation can cause an `IndentationError`.

**Best practice:** Use four spaces per indentation level.

### Cybersecurity relevance

When writing scripts that check user permissions or process security logs, correct indentation is essential because it determines which statements execute under a particular condition.

---

# 3. Comments

Comments explain code and are ignored by the Python interpreter.

```python
# Check whether the account is an administrator
username = "admin"

print(username)
```

A multiline explanation can use several comment lines:

```python
# This script checks a sample login record.
# It does not connect to a real authentication system.
```

Triple-quoted strings are often used for docstrings, which document modules, classes and functions.

```python
def check_login():
    """Check a sample login record."""
    pass
```

### Cybersecurity example

```python
# Record a suspicious login for investigation
ip_address = "192.0.2.10"
```

`192.0.2.10` is an example IP address reserved for documentation.

**Question:** Do comments affect program execution?

**Answer:** Ordinary comments do not execute as Python statements. Docstrings are string objects associated with documented code.

---

# 4. Variables

A variable is a name that refers to a value.

```python
username = "raja"
failed_attempts = 3
is_suspicious = True
```

Here:

- `username` stores a string.
- `failed_attempts` stores an integer.
- `is_suspicious` stores a Boolean.

Python does not require you to declare a variable's type beforehand.

```python
value = 10
value = "security"
```

This is valid because Python allows a variable name to refer to values of different types at different times.

### Variable naming rules

Valid:

```python
username = "admin"
failed_attempts = 5
user_ip = "192.0.2.10"
```

Invalid:

```python
2users = 10
user-name = "admin"
```

Variable names cannot start with a digit or contain a hyphen as part of the name.

Python is case-sensitive:

```python
username = "raja"
Username = "admin"
```

These are two different variable names.

### Cybersecurity example

```python
source_ip = "192.0.2.15"
failed_logins = 7
account_locked = True

print(source_ip)
print(failed_logins)
print(account_locked)
```

**Interview question:** What is the difference between `=` and `==`?

**Answer:**

- `=` assigns a value to a variable.
- `==` compares two values for equality.

---

# 5. Python Data Types

A data type determines the kind of value a variable holds.

| Data type  | Example   | Common use in security      |
| ---------- | --------- | --------------------------- |
| `int`      | `404`     | Status codes, counters      |
| `float`    | `2.5`     | Measurements, durations     |
| `str`      | `"admin"` | Usernames, log messages     |
| `bool`     | `True`    | Security checks             |
| `NoneType` | `None`    | Missing or unavailable data |

### Integer (`int`)

```python
failed_attempts = 5
status_code = 403

print(type(failed_attempts))
```

Output:

```text
<class 'int'>
```

### Float (`float`)

```python
response_time = 1.75
print(type(response_time))
```

### String (`str`)

```python
username = "admin"
ip_address = "192.0.2.15"

print(type(username))
```

Strings represent text, even when they contain digits.

```python
port = "443"
```

This is a string, not an integer.

### Boolean (`bool`)

```python
is_authenticated = True
access_denied = False
```

Boolean values are commonly used in authorization checks and validation.

### None

```python
token = None
```

`None` represents the absence of a value. It is not the same as `0`, `False`, or an empty string.

### Checking a data type

Use `type()`:

```python
value = "403"
print(type(value))
```

Output:

```text
<class 'str'>
```

**Cybersecurity question:** Why does data type matter when processing logs?

**Answer:** A value read from a file or user input is often text. Converting numeric text to an integer is necessary before performing numeric comparisons, counters or calculations.

---

# 6. Taking Input from Users

The `input()` function reads text entered by a user.

```python
username = input("Enter username: ")
print("Username:", username)
```

Example:

```text
Enter username: raja
Username: raja
```

**Important:** `input()` returns a string, even if the user enters digits.

```python
attempts = input("Enter failed login attempts: ")
print(type(attempts))
```

The result is `str`.

### Converting input to an integer

```python
attempts = int(input("Enter failed login attempts: "))
print(attempts + 1)
```

If the user enters `4`, the output is `5`.

### Converting input to a float

```python
response_time = float(input("Enter response time: "))
print(response_time)
```

### Cybersecurity example

```python
username = input("Enter username: ")
attempts = int(input("Enter failed login count: "))

print("Account:", username)
print("Failed attempts:", attempts)
```

This is useful for practicing security-related input processing.

**Security note:** Real applications should validate user input and handle conversion errors. Never assume that input is trustworthy.

---

# 7. Type Conversion

Type conversion changes a value from one data type to another.

Common functions:

- `int()` — converts compatible values to integers.
- `float()` — converts compatible values to floating-point numbers.
- `str()` — converts a value to a string.
- `bool()` — converts a value to a Boolean.

Examples:

```python
port_text = "443"
port_number = int(port_text)

print(port_number)
print(type(port_number))
```

Output:

```text
443
<class 'int'>
```

### Converting a number to a string

```python
status_code = 403
message = "HTTP status: " + str(status_code)

print(message)
```

Output:

```text
HTTP status: 403
```

### Common mistake

```python
attempts = input("Enter attempts: ")
print(attempts + 1)
```

This raises a `TypeError` because `attempts` is a string.

Correct:

```python
attempts = int(input("Enter attempts: "))
print(attempts + 1)
```

However, `int()` raises a `ValueError` if the user enters incompatible text. You will learn to handle this using `try` and `except` later.

---

# 8. Output Using `print()`

The `print()` function displays values.

```python
print("Security check started")
print(10)
print(True)
```

You can print multiple values:

```python
username = "raja"
attempts = 3

print(username, attempts)
```

Output:

```text
raja 3
```

### The `sep` parameter

```python
print("192", "0", "2", "10", sep=".")
```

Output:

```text
192.0.2.10
```

### The `end` parameter

```python
print("Checking", end=" ")
print("logs")
```

Output:

```text
Checking logs
```

By default, `print()` ends with a newline. The `end` parameter changes what is printed at the end.

---

# 9. String Formatting: Concatenation and f-Strings

Strings are especially important when handling usernames, file paths, log messages and HTTP responses.

### Method 1: String concatenation

The `+` operator joins strings.

```python
x = "raja"
y = "mohan"

print(x + y)
```

Output:

```text
rajamohan
```

To add a space:

```python
print(x + " " + y)
```

Output:

```text
raja mohan
```

### Method 2: f-strings

An f-string allows you to insert values into a string using `{}`.

```python
x = "raja"
y = "mohan"

print(f"{x} {y}")
```

Output:

```text
raja mohan
```

### Cybersecurity example

```python
username = "admin"
failed_attempts = 5

print(f"User {username} has {failed_attempts} failed login attempts.")
```

Output:

```text
User admin has 5 failed login attempts.
```

**Interview question:** Why use f-strings?

**Answer:** They make formatted strings more readable and allow expressions to be embedded directly.

**Security note:** Do not construct SQL queries or shell commands by blindly inserting untrusted strings. Use parameterized queries and safe APIs instead.

---

# 10. String Operations and Methods

A string is a sequence of characters.

```python
message = "Suspicious Login Detected"
```

### Accessing characters

Python uses zero-based indexing.

```python
print(message[0])
```

Output:

```text
S
```

Negative indexing counts from the end:

```python
print(message[-1])
```

Output:

```text
d
```

### String slicing

```python
ip_address = "192.0.2.10"

print(ip_address[0:3])
```

Output:

```text
192
```

The starting index is included, but the ending index is excluded.

### Useful string methods

```python
username = "  ADMIN  "

print(username.lower())       # "  admin  "
print(username.upper())       # "  ADMIN  "
print(username.strip())       # "ADMIN"
print(username.replace("A", "X"))
print(username.startswith(" "))
print(username.endswith(" "))
```

Other useful methods include:

```python
log = "ERROR: Login failed"

print(log.find("Login"))
print(log.count("i"))
print(log.split())
print("admin".isalpha())
print("12345".isdigit())
print("admin123".isalnum())
```

### Cybersecurity use case: examining a log entry

```python
log = "2026-10-26 ERROR Failed login"

if "ERROR" in log:
    print("Error event found")
```

Output:

```text
Error event found
```

**Important:** Simple string matching is useful for practice, but production log analysis usually needs structured parsing and more careful matching. Avoid blindly normalizing security-sensitive values, such as passwords or cryptographic tokens.

---

# 11. Operators in Python

Operators perform calculations, comparisons and logical operations.

## 11.1 Arithmetic operators

| Operator | Meaning           | Example  | Result |
| -------- | ----------------- | -------- | ------ |
| `+`      | Addition          | `5 + 2`  | `7`    |
| `-`      | Subtraction       | `5 - 2`  | `3`    |
| `*`      | Multiplication    | `5 * 2`  | `10`   |
| `/`      | Division          | `5 / 2`  | `2.5`  |
| `//`     | Floor division    | `5 // 2` | `2`    |
| `%`      | Modulus/remainder | `5 % 2`  | `1`    |
| `**`     | Exponentiation    | `2 ** 3` | `8`    |

Cybersecurity example:

```python
failed_attempts = 12
threshold = 5

print(failed_attempts % threshold)
```

The remainder is `2`. The modulus operator is useful in general calculations, but a security threshold should usually be checked directly with a comparison.

## 11.2 Comparison operators

Comparison operators return `True` or `False`.

| Operator | Meaning                  |
| -------- | ------------------------ |
| `==`     | Equal to                 |
| `!=`     | Not equal to             |
| `>`      | Greater than             |
| `<`      | Less than                |
| `>=`     | Greater than or equal to |
| `<=`     | Less than or equal to    |

Example:

```python
failed_attempts = 6

print(failed_attempts > 5)
print(failed_attempts == 6)
print(failed_attempts != 0)
```

Output:

```text
True
True
True
```

## 11.3 Logical operators

- `and` — both conditions must be true.
- `or` — at least one condition must be true.
- `not` — reverses a Boolean result.

Example:

```python
is_authenticated = True
is_authorized = False

print(is_authenticated and is_authorized)
print(is_authenticated or is_authorized)
print(not is_authorized)
```

Output:

```text
False
True
True
```

Cybersecurity example:

```python
is_authenticated = True
is_authorized = True

if is_authenticated and is_authorized:
    print("Access permitted by this example policy")
```

Authentication establishes identity; authorization determines what that identity is allowed to do. Real access-control systems need more than two Boolean variables.

## 11.4 Assignment operators

```python
attempts = 1
attempts += 1
attempts -= 1
attempts *= 2
```

These operators update the value of a variable.

For example, `attempts += 1` is equivalent to `attempts = attempts + 1`.

## 11.5 Membership operators

The `in` and `not in` operators check whether a value exists within a sequence or collection.

```python
log = "ERROR: Failed login"

print("ERROR" in log)
print("SUCCESS" not in log)
```

Output:

```text
True
True
```

## 11.6 Identity operators

`is` and `is not` check object identity, not general value equality.

```python
token = None

print(token is None)
```

Output:

```text
True
```

Use `==` to compare values and `is None` to check whether a value is `None`.

---

# 12. Conditional Statements

Conditional statements allow Python to make decisions based on whether expressions are true or false.

## 12.1 The `if` statement

```python
failed_attempts = 6

if failed_attempts > 5:
    print("Investigate repeated failed logins")
```

The statement inside the block executes only if the condition is true.

## 12.2 The `if-else` statement

```python
status_code = 403

if status_code == 200:
    print("Request succeeded")
else:
    print("Request was not successful")
```

Output:

```text
Request was not successful
```

Note that HTTP status code `403` means forbidden, whereas a `404` indicates not found. The example's `else` groups all non-200 responses together; it does not mean every other response is an error.

## 12.3 The `if-elif-else` statement

Use `elif` when multiple conditions need to be checked.

```python
status_code = 403

if status_code == 200:
    print("Success")
elif status_code == 401:
    print("Authentication required or failed")
elif status_code == 403:
    print("Access forbidden")
else:
    print("Other HTTP response")
```

Python checks conditions in order and executes the first matching branch.

## 12.4 Nested conditions

A nested condition is an `if` statement inside another `if` statement.

```python
is_authenticated = True
is_authorized = True

if is_authenticated:
    if is_authorized:
        print("Access permitted")
    else:
        print("Access denied: insufficient permissions")
else:
    print("Access denied: authentication required")
```

Nested conditions can model multi-step decisions, but deeply nested code can be harder to maintain.

## 12.5 Combining conditions

```python
failed_attempts = 7
account_active = True

if failed_attempts >= 5 and account_active:
    print("Review account for suspicious activity")
```

This example flags a condition for review. It does not prove an attack is occurring.

Another example:

```python
status_code = 401

if status_code == 401 or status_code == 403:
    print("Review authentication or access-control response")
```

## 12.6 Using `not`

```python
is_authenticated = False

if not is_authenticated:
    print("Authentication required")
```

The block executes because `not False` is `True`.

## 12.7 Checking input

```python
username = input("Enter username: ")

if username == "":
    print("Username cannot be empty")
else:
    print("Username received")
```

To also reject whitespace-only input:

```python
username = input("Enter username: ")

if not username.strip():
    print("Username cannot be empty")
else:
    print("Username received")
```

This validates an input field, not the actual identity of a user.

---

# 13. Truthy and Falsy Values

Python treats certain values as false in conditions.

Common falsy values include:

- `False`
- `None`
- `0` and `0.0`
- `""` (empty string)
- Empty collections, such as `[]`, `{}` and `()`

Many other values are truthy.

Example:

```python
username = ""

if username:
    print("Username provided")
else:
    print("Username missing")
```

Output:

```text
Username missing
```

**Interview question:** What is the difference between `if username` and `if username == ""`?

**Answer:** `if username` checks whether the value is truthy. `if username == ""` specifically checks whether it is an empty string. A string containing spaces is truthy, so use `strip()` if whitespace-only input should count as empty.

---

# 14. Cybersecurity Mini Projects Using These Concepts

These exercises use only sample data and do not attack real systems.

## Project 1: Password-length checker

```python
password = input("Enter a sample password: ")

if len(password) < 12:
    print("Password is shorter than the example policy requires")
else:
    print("Password meets the minimum-length check")
```

This checks length only. It does not prove a password is strong, unique or uncompromised. Real password policies should follow current security guidance.

## Project 2: Suspicious login indicator

```python
username = "admin"
failed_attempts = 8
source_ip = "192.0.2.15"

if failed_attempts >= 5:
    print(f"Review login activity for {username}")
    print(f"Source IP: {source_ip}")
else:
    print("Attempt count is below the example threshold")
```

This simulates a basic rule-based detection. Real monitoring should consider time windows, account context and false positives.

## Project 3: HTTP status classifier

```python
status_code = int(input("Enter an HTTP status code: "))

if 200 <= status_code < 300:
    print("Successful response")
elif 300 <= status_code < 400:
    print("Redirection")
elif status_code == 401:
    print("Authentication required or failed")
elif status_code == 403:
    print("Forbidden")
elif status_code == 404:
    print("Resource not found")
elif 400 <= status_code < 500:
    print("Other client error")
elif 500 <= status_code < 600:
    print("Server error")
else:
    print("Outside the usual HTTP status ranges")
```

For a robust program, validate the input and handle invalid values. The script classifies a status code; it does not make a request to a website.

## Project 4: Port-number validation

```python
port = int(input("Enter a port number: "))

if 1 <= port <= 65535:
    print("Port number is in the valid TCP/UDP range")
else:
    print("Port number is outside the valid range")
```

This checks the numerical range only. It does not check whether a service is listening or whether the port is reachable.

## Project 5: Detect a keyword in a sample log

```python
log_entry = input("Enter a sample log entry: ")

if "failed login" in log_entry.lower():
    print("Possible failed-login event detected")
else:
    print("Keyword not found")
```

This is a basic string-matching exercise. A production log analyzer should account for structured log formats, event identifiers and variations in wording.

---

# 15. Important Cybersecurity Concepts to Understand Alongside Python

| Concept            | What to learn                                                 |
| ------------------ | ------------------------------------------------------------- |
| Authentication     | How systems verify identity                                   |
| Authorization      | How permissions are enforced                                  |
| Least privilege    | Giving only the permissions needed                            |
| Input validation   | Checking type, format and acceptable range                    |
| Logging            | Recording events for investigation                            |
| False positive     | Benign activity incorrectly flagged as suspicious             |
| False negative     | Malicious activity that a detector misses                     |
| HTTP status codes  | Understanding web-server responses                            |
| IP addresses       | Identifying network endpoints                                 |
| Ports              | Identifying transport-layer service endpoints                 |
| Secrets management | Keeping passwords, API keys and tokens out of source code     |
| Safe testing       | Practicing only on systems you own or have permission to test |

Python helps implement security checks, but it does not replace knowledge of networking, operating systems, web technologies and security principles.

---

# 16. Interview Questions and Answers

### Basic Python questions

**Q1. What is Python?**

Python is a high-level, general-purpose programming language commonly used for scripting, automation, data processing and security tooling.

**Q2. What is a variable?**

A variable is a name that refers to a value.

**Q3. Is Python statically or dynamically typed?**

Python is dynamically typed. You do not need to declare a variable's type in advance.

**Q4. What is the difference between `int` and `float`?**

An integer represents whole numbers; a float represents floating-point numbers.

**Q5. What does `type()` do?**

It returns the type of an object.

**Q6. What does `input()` return?**

A string.

**Q7. What is type conversion?**

Changing a value from one type to another, such as converting `"443"` to `443` using `int()`.

**Q8. What is indentation?**

Leading whitespace that defines code blocks in Python.

**Q9. What is a string?**

A sequence of characters represented using quotes.

**Q10. What is an f-string?**

A string-formatting method that embeds expressions inside `{}`.

### Operators and conditions

**Q11. What is the difference between `=` and `==`?**

`=` assigns a value; `==` compares values.

**Q12. What is the difference between `and` and `or`?**

`and` requires both conditions to be true; `or` requires at least one.

**Q13. What does `not` do?**

It reverses a Boolean truth value.

**Q14. What is the difference between `if` and `elif`?**

`if` begins a conditional chain; `elif` checks another condition if the earlier branch was not selected.

**Q15. What is a nested `if`?**

An `if` statement inside another conditional block.

**Q16. What does `%` do?**

It returns the remainder of a division.

**Q17. What is the difference between `in` and `==`?**

`in` checks membership; `==` checks value equality.

**Q18. What does `is None` check?**

Whether a value is the special object `None`.

### Cybersecurity-focused questions

**Q19. Why is Python useful in cybersecurity?**

It can automate repetitive checks, analyze logs, process indicators and interact with APIs.

**Q20. How can Python identify repeated failed logins?**

It can count failed-login events and flag accounts that cross a defined threshold within a time window.

**Q21. Is a failed login automatically evidence of an attack?**

No. Users can mistype passwords, and automated systems can generate legitimate failures. Context is necessary.

**Q22. Why validate user input?**

To ensure that data meets expected types, formats and constraints and to reduce errors and security risks.

**Q23. Why is string concatenation risky when constructing SQL?**

Untrusted input can alter the query. Parameterized queries should be used instead.

**Q24. Can a Python script detect every malicious IP address?**

No. Detection depends on available data, detection rules, context and intelligence sources.

**Q25. What is the difference between authentication and authorization?**

Authentication verifies identity; authorization determines permitted actions.

---

# 17. Practice Questions With Answers

Try to solve each question before looking at the answer.

## Section A — Predict the output

**Question 1**

```python
x = "raja"
y = "mohan"

print(x + y)
print(f"{x} {y}")
```

**Answer:**

```text
rajamohan
raja mohan
```

**Question 2**

```python
value = "443"
print(type(value))
```

**Answer:** `str`, because the value is enclosed in quotes.

**Question 3**

```python
attempts = 5
print(attempts > 3 and attempts < 10)
```

**Answer:** `True`.

**Question 4**

```python
status_code = 403

if status_code == 200:
    print("Success")
else:
    print("Not success")
```

**Answer:** `Not success`.

**Question 5**

```python
username = ""

if username:
    print("Provided")
else:
    print("Missing")
```

**Answer:** `Missing`.

## Section B — Find the mistake

**Question 6**

```python
attempts = input("Attempts: ")
print(attempts + 1)
```

**Answer:** The input is a string. Convert it to an integer before performing arithmetic.

**Question 7**

```python
if attempts = 5:
    print("Five attempts")
```

**Answer:** Use `==` for comparison, not `=`.

Correct:

```python
if attempts == 5:
    print("Five attempts")
```

**Question 8**

```python
username = "admin"

if username == "admin":
print("Administrator")
```

**Answer:** The `print()` statement needs indentation.

## Section C — Write a program

**Question 9:** Write a program that checks whether a failed-login count exceeds five.

```python
attempts = 6

if attempts > 5:
    print("Review required")
else:
    print("Below threshold")
```

**Question 10:** Write a program that identifies HTTP status `403`.

```python
status_code = 403

if status_code == 403:
    print("Forbidden")
else:
    print("Different status code")
```

**Question 11:** Write a program that rejects an empty username.

```python
username = input("Username: ")

if not username.strip():
    print("Invalid username")
else:
    print("Username received")
```

**Question 12:** Write a program that checks whether a port is within the valid numerical range.

```python
port = 443

if 1 <= port <= 65535:
    print("Valid numerical range")
else:
    print("Invalid numerical range")
```

**Question 13:** Write a program that checks whether a log entry contains the word `ERROR`, regardless of letter case.

```python
log_entry = "error: access denied"

if "error" in log_entry.lower():
    print("Error found")
else:
    print("Error not found")
```

---

# 18. Revision Checklist

Before moving to the next Python topic, make sure you can explain and use each item without copying an example.

- Print output with `print()`.
- Write comments and use correct indentation.
- Create variables with meaningful names.
- Explain `int`, `float`, `str`, `bool` and `None`.
- Take input with `input()`.
- Convert input using `int()`, `float()` and `str()`.
- Join strings using `+` and f-strings.
- Use indexing, slicing and common string methods.
- Apply arithmetic, comparison and logical operators.
- Explain `in`, `not in`, `is` and `is not`.
- Write `if`, `if-else` and `if-elif-else` statements.
- Combine conditions using `and`, `or` and `not`.
- Validate a username, port number or sample status code.
- Analyze a sample log entry with conditional statements.

---

# 19. What to Learn Next

Once you are comfortable with these topics, follow this sequence:

1. **Loops:** `for`, `while`, `break`, `continue` — process multiple log entries and repeated events.
2. **Collections:** Lists, tuples, sets and dictionaries — store and organize IP addresses, event records and indicators.
3. **Functions:** Write reusable security checks.
4. **Exception handling:** Use `try`, `except` and `finally` to handle invalid input and file errors.
5. **File handling:** Read and write logs and configuration files.
6. **Modules and packages:** Organize code and use Python libraries.
7. **Regular expressions:** Extract IP addresses, timestamps and patterns from logs.
8. **Networking:** Learn sockets, DNS, HTTP and packet analysis.
9. **Security projects:** Build a log analyzer, file-integrity checker and authorized network-monitoring utilities.

**Recommended study method:** Understand each concept, type the example yourself, predict the output, change the input, and then build a small cybersecurity exercise using that concept. Do not move forward merely because you have read the notes.

Your next step is to practice these topics until you can write the examples without referring to the notes. Then move to loops, because loops are essential for analyzing multiple log entries, IP addresses and security events efficiently.

---

# Python for Cybersecurity — Remaining Fundamentals

> **Focus:** Control-Flow Statements (Loops), Built-in Functions, String Methods for Security Analysis, Practice Quiz, and Hands-on Coding Exercises.  
> **Key Principle:** `for` and `while` are loops, not conditional statements. They control repetition, while `if`, `elif`, and `else` control decisions.

---

# Part 1: Remaining Control-Flow Statements (Loops)

## 1. The `for` Loop

A `for` loop repeats code for each item in a sequence or other iterable (such as a string, list, tuple, or range).

### Basic syntax:

```python
for item in sequence:
    # code to repeat
    print(item)
```

### Example:

```python
users = ["raja", "admin", "guest"]

for user in users:
    print(user)
```

Output:

```text
raja
admin
guest
```

### How it works:
1. Python retrieves the first item: `"raja"`.
2. It executes the indented code block.
3. It retrieves the next item and repeats the process.
4. It stops when there are no more items left in the sequence.

### Cybersecurity example: Analyze multiple log entries

```python
logs = [
    "Login successful",
    "Failed login",
    "Failed login",
    "Password changed"
]

for log in logs:
    if "failed login" in log.lower():
        print("Suspicious event:", log)
```

Output:

```text
Suspicious event: Failed login
Suspicious event: Failed login
```

*This demonstrates identifying security log entries by matching keywords during iteration.*

---

## 2. `range()` with a `for` Loop

`range()` generates an immutable sequence of integers. It is commonly used to repeat an operation a specific number of times without creating an explicit list in memory.

```python
for i in range(5):
    print(i)
```

Output:

```text
0
1
2
3
4
```

> **Note:** `range(5)` starts at index `0` and stops before `5` (contains 5 elements: 0 through 4).

### Three common forms:

| Expression | Values generated | Description |
| --- | --- | --- |
| `range(stop)` | `0, 1, 2, 3, 4` | Starts at `0`, increments by `1`, stops before `stop` |
| `range(start, stop)` | `1, 2, 3, 4` | Starts at `start`, increments by `1`, stops before `stop` |
| `range(start, stop, step)` | `2, 4, 6, 8, 10` | Starts at `start`, increments by `step`, stops before `stop` |

### Cybersecurity example: Simulated attempt counter

```python
for attempt in range(1, 6):
    print(f"Simulated attempt number: {attempt}")
```

Output:

```text
Simulated attempt number: 1
Simulated attempt number: 2
Simulated attempt number: 3
Simulated attempt number: 4
Simulated attempt number: 5
```

---

## 3. The `while` Loop

A `while` loop repeats statements as long as its conditional expression evaluates to `True`.

```python
count = 1

while count <= 5:
    print(count)
    count += 1
```

Output:

```text
1
2
3
4
5
```

### How it works:
1. `count` is initialized to `1`.
2. Python evaluates `count <= 5`.
3. If `True`, the indented loop body executes.
4. `count += 1` increments the counter.
5. When `count` reaches `6`, the condition evaluates to `False`, and execution continues after the loop.

### Cybersecurity example: Process sample security events

```python
event_count = 1

while event_count <= 3:
    print(f"Processing security event {event_count}")
    event_count += 1
```

Output:

```text
Processing security event 1
Processing security event 2
Processing security event 3
```

> **Caution:** Always ensure that the condition will eventually evaluate to `False`, or use a `break` statement. Otherwise, the program enters an infinite loop.

---

## 4. Difference Between `for` and `while`

| Feature | `for` loop | `while` loop |
| --- | --- | --- |
| **Operation** | Iterates over items of a sequence or iterable | Repeats while a boolean condition remains `True` |
| **Primary Use** | Processing collections, lines, or log entries | Polling status, waiting for an event, or condition-driven loops |
| **Iterations** | Usually known or bounded by collection size | Often dynamic or indefinite until a condition changes |
| **Control** | Direct iteration over strings, lists, files, ranges | Requires explicit initialization and condition updates |

---

## 5. The `break` Statement

The `break` statement immediately terminates the innermost enclosing loop.

```python
for number in range(1, 10):
    if number == 5:
        break
    print(number)
```

Output:

```text
1
2
3
4
```

### Cybersecurity example: Alert on the first critical event

```python
logs = [
    "Login successful",
    "Password changed",
    "CRITICAL: Security alert",
    "Failed login"
]

for log in logs:
    if "CRITICAL" in log:
        print("Critical event found:", log)
        break
```

Output:

```text
Critical event found: CRITICAL: Security alert
```

> **Practical note:** Stopping at the first critical incident is useful for emergency fail-safes. In full auditing and SIEM ingestion, you typically record all events rather than breaking early.

---

## 6. The `continue` Statement

The `continue` statement skips the remainder of the current iteration and immediately begins the next iteration of the loop.

```python
for number in range(1, 6):
    if number == 3:
        continue
    print(number)
```

Output:

```text
1
2
4
5
```

### Cybersecurity example: Filter out routine informational messages

```python
logs = [
    "INFO: Service started",
    "ERROR: Failed login",
    "INFO: Health check passed",
    "ERROR: Invalid token"
]

for log in logs:
    if "INFO" in log:
        continue
    print("Review:", log)
```

Output:

```text
Review: ERROR: Failed login
Review: ERROR: Invalid token
```

---

## 7. The `pass` Statement

The `pass` statement is a null operation. When it executes, nothing happens. It serves as a syntactic placeholder when Python requires an indented statement block.

```python
for log in ["INFO", "ERROR"]:
    if log == "ERROR":
        pass  # Placeholder: Implement incident ticketing logic here later
    else:
        print(log)
```

Output:

```text
INFO
```

### Comparing Loop Control Keywords:

| Keyword | Behavior | Effect on Loop |
| --- | --- | --- |
| `break` | Aborts the loop entirely | Exits loop immediately |
| `continue` | Skips remaining code in current pass | Moves to next iteration |
| `pass` | Executes no-op | Loop proceeds without interruption |

---

## 8. Nested Loops

A nested loop is a loop located inside the body of another loop. The inner loop executes all its iterations for every single cycle of the outer loop.

```python
for user in ["admin", "guest"]:
    for event in ["login", "logout"]:
        print(user, event)
```

Output:

```text
admin login
admin logout
guest login
guest logout
```

### Cybersecurity example: Audit users against security events

```python
users = ["admin", "guest"]
events = ["failed login", "password reset"]

for user in users:
    for event in events:
        print(f"User: {user}, Event: {event}")
```

> **Performance warning:** If an outer list has $N$ elements and the inner list has $M$ elements, the inner block executes $N \times M$ times ($O(N \times M)$ complexity). When handling millions of SIEM log records, avoid deep nesting where hash lookups (`set` / `dict`) can be used instead.

---

## 9. The `else` Clause with Loops

Python loops support an optional `else` block. The `else` clause executes **only when the loop completes all iterations normally** (i.e., without hitting a `break` statement).

### Normal completion:

```python
for number in range(3):
    print(number)
else:
    print("Loop completed normally")
```

Output:

```text
0
1
2
Loop completed normally
```

### Premature exit via `break`:

```python
for number in range(5):
    if number == 2:
        break
else:
    print("Loop completed without break")
```

*(Nothing is printed by the `else` block because `break` was encountered).*

### Cybersecurity application: Threat indicator search

```python
known_ips = ["192.0.2.10", "192.0.2.20"]
target_ip = "192.0.2.30"

for ip in known_ips:
    if ip == target_ip:
        print("Match found in threat feed")
        break
else:
    print("No match found in this list")
```

Output:

```text
No match found in this list
```

*Note: Absence from a local blocklist does not guarantee the IP is benign; it only confirms it was not matched in the sample dataset.*

---

# Part 2: Python Built-in Functions

Built-in functions are always accessible in Python without requiring any `import` statement.

## 1. Essential Built-in Functions Overview

- `print()` — Outputs data to stdout.
- `input()` — Reads user input as a string.
- `len()` — Returns collection size or string length.
- `type()` — Returns the data type class of an object.
- `range()` — Generates an arithmetic sequence of integers.
- `int()`, `float()`, `str()`, `bool()` — Perform type conversions.
- `help()` — Invokes built-in interactive documentation.

---

## 2. `print()` in Depth

```python
print("Security started")
print(10, 20, 30)
print("User:", "admin")
print("192", "0", "2", "10", sep=".")
print("Checking", end=" ")
print("logs")
```

Output:

```text
Security started
10 20 30
User: admin
192.0.2.10
Checking logs
```

- `sep`: String inserted between values (defaults to space `" "`).
- `end`: String appended at the end (defaults to newline `"\n"`).

---

## 3. `input()`

Reads a line from standard input, strips the trailing newline, and returns it as a string:

```python
username = input("Enter username: ")
print(f"Received username: {username}")
```

---

## 4. `len()`

Returns the number of elements in an object:

```python
print(len("Python"))                    # 6 characters
print(len([10, 20, 30]))                # 3 items in list
print(len({"admin", "guest"}))          # 2 items in set
print(len({"username": "raja"}))        # 1 key-value pair in dict
```

### Cybersecurity example:

```python
password = "ExamplePassword123!"
log_entries = ["ERROR", "INFO", "WARNING"]

print(len(password))     # 19 characters
print(len(log_entries))  # 3 records
```

> **Security note:** `len(password)` measures length only. Effective credential hygiene also requires entropy, dictionary checks, and checking against leaked credential databases.

---

## 5. `type()` and `isinstance()`

- `type()` inspects the concrete runtime type of an object.
- `isinstance()` checks if an object is an instance of a class or a tuple of classes.

```python
print(type(10))        # <class 'int'>
print(type("admin"))   # <class 'str'>
print(type(True))      # <class 'bool'>

port = 443
print(isinstance(port, int))   # True
print(isinstance(port, str))   # False
```

> **Python quirk:** In Python, `bool` is a subclass of `int`. Therefore, `isinstance(True, int)` evaluates to `True`.

---

## 6. `range()`

Generates integer sequences on demand:

```python
print(list(range(5)))          # [0, 1, 2, 3, 4]
print(list(range(1, 5)))       # [1, 2, 3, 4]
print(list(range(1, 10, 2)))   # [1, 3, 5, 7, 9]
```

---

## 7. `sum()`, `min()`, and `max()`

Aggregates numeric iterables:

```python
attempts = [2, 4, 1, 7, 3]

print(sum(attempts))  # 17
print(min(attempts))  # 1
print(max(attempts))  # 7
```

### Cybersecurity telemetry example:

```python
failed_logins = [2, 8, 3, 6, 1]

total = sum(failed_logins)
highest = max(failed_logins)

print(f"Total failed logins: {total}")
print(f"Highest count in a sample: {highest}")
```

Output:

```text
Total failed logins: 20
Highest count in a sample: 8
```

---

## 8. `abs()` and `round()`

```python
print(abs(-10))           # 10 (absolute value)
print(round(3.14159, 2))  # 3.14 (rounded to 2 decimal places)
```

---

## 9. `sorted()` and `reversed()`

`sorted()` returns a new list containing all items in ascending or descending order without mutating the original collection:

```python
attempts = [7, 2, 5, 1]
print(sorted(attempts))                # [1, 2, 5, 7]
print(sorted(attempts, reverse=True))  # [7, 5, 2, 1]
```

### Cybersecurity example:

```python
event_counts = [12, 3, 19, 8]
print(sorted(event_counts, reverse=True))  # [19, 12, 8, 3]
```

`reversed()` returns a reverse iterator over a sequence:

```python
for number in reversed([1, 2, 3]):
    print(number)
```

Output:

```text
3
2
1
```

---

## 10. `enumerate()`

Returns an iterator yielding pairs containing an index (starting from `0` or an optional `start` parameter) and the item value:

```python
logs = ["INFO", "ERROR", "WARNING"]

for index, log in enumerate(logs, start=1):
    print(f"Line {index}: {log}")
```

Output:

```text
Line 1: INFO
Line 2: ERROR
Line 3: WARNING
```

*Ideal for tracking log line numbers during incident triage.*

---

## 11. `any()` and `all()`

- `any()` returns `True` if **at least one** element of the iterable evaluates to truthy.
- `all()` returns `True` if **every** element in the iterable evaluates to truthy.

```python
checks = [True, False, True]
print(any(checks))  # True
print(all(checks))  # False
```

### Cybersecurity policy verification:

```python
required_checks = {
    "authenticated": True,
    "authorized": True,
    "account_active": False
}

print("All conditions passed:", all(required_checks.values()))
```

Output:

```text
All conditions passed: False
```

---

## 12. Collection Constructors: `list()`, `tuple()`, `set()`, `dict()`

```python
print(list("abc"))                      # ['a', 'b', 'c']
print(tuple([1, 2, 3]))                 # (1, 2, 3)
print(set(["admin", "guest", "admin"]))  # {'admin', 'guest'}
print(dict(username="raja", port=443))  # {'username': 'raja', 'port': 443}
```

| Type | Order | Mutability | Uniqueness |
| --- | --- | --- | --- |
| `list` | Ordered | Mutable | Allows duplicates |
| `tuple` | Ordered | Immutable | Allows duplicates |
| `set` | Unordered | Mutable | Enforces uniqueness |
| `dict` | Ordered (as of 3.7+) | Mutable | Unique keys |

### Cybersecurity deduplication example:

```python
suspicious_ips = ["192.0.2.10", "192.0.2.10", "192.0.2.20"]
unique_ips = set(suspicious_ips)

print("Unique IPs:", unique_ips)
print("Count:", len(unique_ips))
```

Output:

```text
Unique IPs: {'192.0.2.10', '192.0.2.20'}
Count: 2
```

---

## 13. `zip()`

Combines elements from multiple iterables element-wise into tuples:

```python
users = ["admin", "guest", "raja"]
attempts = [5, 1, 3]

for user, count in zip(users, attempts):
    print(f"User: {user} | Attempts: {count}")
```

Output:

```text
User: admin | Attempts: 5
User: guest | Attempts: 1
User: raja | Attempts: 3
```

> **Warning:** By default, `zip()` truncates to the length of the shortest iterable. When parsing log records, mismatched lengths can cause silent data loss. In Python 3.10+, use `zip(..., strict=True)` to raise a `ValueError` on mismatched sequence lengths.

---

## 14. `ord()` and `chr()`

- `ord(char)`: Returns the Unicode code point (integer) of a character.
- `chr(code)`: Converts an integer code point back to its character representation.

```python
print(ord("A"))  # 65
print(chr(65))   # 'A'
```

*Useful for analyzing character sets and encoding schemes (e.g., shellcode or XOR decoding).*

---

## 15. `open()`

Opens files for stream processing. Always pair with the `with` statement (context manager) to guarantee file closure:

```python
with open("sample.log", "r", encoding="utf-8") as file:
    for line in file:
        print(line.strip())
```

---

## 16. Quick Reference: Other Common Built-ins

| Function | Purpose | Example |
| --- | --- | --- |
| `format()` | Formats values | `format(3.14159, ".2f")` |
| `pow()` | Exponentiation ($x^y \pmod z$) | `pow(2, 3)` |
| `divmod()` | Returns `(quotient, remainder)` | `divmod(10, 3)` $\to (3, 1)$ |
| `iter()` | Returns an iterator object | `iter([1, 2, 3])` |
| `next()` | Advances an iterator | `next(it)` |
| `id()` | Returns memory address identity | `id(username)` |
| `dir()` | Lists object attributes and methods | `dir("text")` |
| `help()` | Displays interactive documentation | `help(str)` |

> [!CAUTION]
> ### Security Warning: Never Use `eval()` or `exec()` on Untrusted Input
> `eval()` and `exec()` execute arbitrary Python code passed as strings. If untrusted input (e.g. from HTTP headers, log files, or user forms) reaches them, an attacker can achieve **Remote Code Execution (RCE)** on your host.

---

# Part 3: Built-in Functions vs. String Methods

A common interview question explores the difference between built-in functions and methods:

- **Built-in functions** are global functions invoked with arguments: `func(object)`.
- **String methods** are functions tied to the `str` class, invoked on string objects via dot notation: `object.method()`.

| Built-in Function | String Method |
| --- | --- |
| `len(text)` | `text.lower()` |
| `str(value)` | `text.upper()` |
| `type(value)` | `text.strip()` |
| `sorted(values)` | `text.split()` |
| `print(text)` | `text.replace("a", "b")` |

### Key String Methods for Security Log Parsing:

```python
log = "  ERROR: Failed Login  "

print(log.lower())                   # "  error: failed login  "
print(log.upper())                   # "  ERROR: FAILED LOGIN  "
print(log.strip())                   # "ERROR: Failed Login"
print(log.replace("ERROR", "WARN"))  # "  WARN: Failed Login  "
print(log.split())                   # ['ERROR:', 'Failed', 'Login']
print(log.startswith("  ERROR"))     # True
print(log.endswith("  "))            # True
print(log.count("i"))                # 1
print(log.find("Failed"))            # 9
```

### Complete Example: Counting Error Records

```python
logs = [
    "INFO: Server started",
    "ERROR: Failed login",
    "WARNING: Retry detected",
    "ERROR: Invalid token"
]

error_count = 0

for log in logs:
    if "ERROR" in log:
        error_count += 1

print(f"Total error records: {error_count}")
```

Output:

```text
Total error records: 2
```

---

# Part 4: Practice Quiz

### 1. What is the output of `len("cyber security")`?
- A) 13
- B) 14
- C) 15  
**Answer:** **B) 14** — The space character is counted as an element.

---

### 2. Which statement exits a loop immediately?
- A) `continue`
- B) `pass`
- C) `break`  
**Answer:** **C) `break`** — `break` terminates loop execution immediately.

---

### 3. What does `range(1, 5)` produce?
- A) 1, 2, 3, 4, 5
- B) 1, 2, 3, 4
- C) 0, 1, 2, 3, 4  
**Answer:** **B) 1, 2, 3, 4** — `range(start, stop)` stops strictly before the `stop` value.

---

### 4. What does `continue` do inside a loop?
- A) Stops the entire program
- B) Skips to the next iteration
- C) Does nothing  
**Answer:** **B) Skips to the next iteration** — It bypasses remaining statements in the current iteration.

---

### 5. Which function returns the largest number in a collection?
- A) `sum()`
- B) `max()`
- C) `len()`  
**Answer:** **B) `max()`**

---

### 6. What does `any([False, True, False])` return?
- A) `True`
- B) `False`
- C) `None`  
**Answer:** **A) `True`** — `any()` evaluates to `True` if at least one item is truthy.

---

### 7. What does `input()` always return?
- A) An integer
- B) A Boolean
- C) A string  
**Answer:** **C) A string**

---

### 8. Which expression checks whether a string has no non-whitespace characters?
- A) `not text.strip()`
- B) `text.upper()`
- C) `len(text) > 0`  
**Answer:** **A) `not text.strip()`** — `.strip()` eliminates whitespace; if empty, it evaluates to falsy, and `not` flips it to `True`.

---

# Part 5: Cybersecurity Hands-on Coding Exercises

### Exercise 1: Count Failed Logins
**Task:** Given a list of sample log entries, count how many contain `"failed login"`, ignoring case.

```python
logs = [
    "User admin: Failed login from 192.0.2.1",
    "User guest: Login successful",
    "User root: FAILED LOGIN from 198.51.100.2",
    "User test: Failed login from 203.0.113.5",
    "User admin: Password reset requested",
    "User analyst: Login successful",
    "User dev: failed login from 192.0.2.9",
    "User dev: Login successful",
    "System: Backup completed",
    "User audit: Logout"
]

failed_count = 0
for entry in logs:
    if "failed login" in entry.lower():
        failed_count += 1

print(f"Total failed logins: {failed_count}")
```

Output:

```text
Total failed logins: 4
```

---

### Exercise 2: Find the Highest Attempt Count
**Task:** Given `[4, 12, 2, 9, 6]`, display the largest value using `max()`.

```python
attempts = [4, 12, 2, 9, 6]
highest_attempt = max(attempts)

print(f"Highest attack attempts detected: {highest_attempt}")
```

Output:

```text
Highest attack attempts detected: 12
```

---

### Exercise 3: Validate Port Numbers
**Task:** Read sample port numbers and determine whether each falls inside the valid TCP/UDP range ($1 - 65535$).

```python
sample_ports = [80, 443, 0, 8080, 70000]

for port in sample_ports:
    if 1 <= port <= 65535:
        print(f"Port {port}: Valid port number")
    else:
        print(f"Port {port}: Invalid port number (Out of range)")
```

Output:

```text
Port 80: Valid port number
Port 443: Valid port number
Port 0: Invalid port number (Out of range)
Port 8080: Valid port number
Port 70000: Invalid port number (Out of range)
```

---

### Exercise 4: Skip Informational Messages
**Task:** Use `continue` to skip log entries containing `"INFO"` and print the rest.

```python
log_stream = [
    "INFO: Service agent initialized",
    "WARN: Disk capacity exceeding 85%",
    "INFO: Heartbeat check healthy",
    "ALERT: Unauthorized access attempt",
    "INFO: Scheduled sync completed"
]

for record in log_stream:
    if "INFO" in record:
        continue
    print("Action Required:", record)
```

Output:

```text
Action Required: WARN: Disk capacity exceeding 85%
Action Required: ALERT: Unauthorized access attempt
```

---

### Exercise 5: Locate a Threat Indicator with Early Exit
**Task:** Search a list of sample IP addresses and use `break` as soon as the target indicator is detected.

```python
ip_feed = ["198.51.100.12", "203.0.113.45", "192.0.2.88", "198.51.100.99"]
target_malicious_ip = "192.0.2.88"

for ip in ip_feed:
    if ip == target_malicious_ip:
        print(f"[ALERT] Malicious IP {ip} matched in threat feed!")
        break
else:
    print("Indicator not found in current feed.")
```

Output:

```text
[ALERT] Malicious IP 192.0.2.88 matched in threat feed!
```

---

### Exercise 6: Summarize Log Telemetry
**Task:** Use `len()`, `sum()`, and a `for` loop to summarize sample security event counts.

```python
hourly_alerts = [5, 14, 2, 8, 21, 3]

total_hours = len(hourly_alerts)
total_alerts = sum(hourly_alerts)
peak_alerts = max(hourly_alerts)

print("--- Telemetry Summary ---")
print(f"Monitoring Window: {total_hours} hours")
print(f"Total Alerts: {total_alerts}")
print(f"Peak Hourly Volume: {peak_alerts}")

above_threshold_count = 0
threshold = 10

for alerts in hourly_alerts:
    if alerts > threshold:
        above_threshold_count += 1

print(f"Hours Exceeding Threshold ({threshold}): {above_threshold_count}")
```

Output:

```text
--- Telemetry Summary ---
Monitoring Window: 6 hours
Total Alerts: 53
Peak Hourly Volume: 21
Hours Exceeding Threshold (10): 2
```

---

# What Comes After These Topics?

With control-flow loops and built-in functions mastered, continue along this cybersecurity development progression:

1. **Collections Deep Dive:** Lists, tuples, sets, and dictionaries (hash tables, fast lookups, structured event mapping).
2. **Functions (`def`, parameters, return values):** Encapsulating logic into modular, testable security tools.
3. **Exception Handling (`try`, `except`, `finally`):** Resilient network scripts that don't crash on connection resets or invalid input.
4. **File I/O & Log Processing:** Reading Apache, Nginx, syslog, and CSV/JSON log dumps efficiently.
5. **Modules & Packages:** Using `os`, `sys`, `subprocess`, `requests`, and standard libraries.
6. **Regular Expressions (`re`):** Extracting IPv4/IPv6 addresses, hashes, CVEs, and user agents with regex.
7. **Networking & Sockets:** Raw socket programming, port scanning, and API interactions.
8. **Hands-on Security Projects:** Log parsing SIEM mini-engines, file integrity checkers (FIM), and automated scanner reporting.

---

# Python Functions — Complete Beginner Notes for Cybersecurity

A function is a reusable block of code that performs a specific task. Instead of writing the same code repeatedly, you define a function once and call it whenever you need it.

In cybersecurity, functions help you build modular, reusable tools for log analysis, input validation, file integrity checks, suspicious activity detection, and security automation.

---

## 1. Why Do We Need Functions?

Without functions, you are forced to repeat the same logic across your scripts:

```python
username = "admin"
print(f"Checking user: {username}")

username = "guest"
print(f"Checking user: {username}")

username = "raja"
print(f"Checking user: {username}")
```

With a function:

```python
def check_user(username):
    print(f"Checking user: {username}")

check_user("admin")
check_user("guest")
check_user("raja")
```

Output:

```text
Checking user: admin
Checking user: guest
Checking user: raja
```

**Key Benefit:** Functions follow the **DRY (Don't Repeat Yourself)** principle, allowing you to reuse the exact same logic with different inputs and data streams.

---

## 2. Function Syntax

### Anatomy of a Python function:

```python
def check_login(username):
    print(username)

check_login("admin")
```

- `def`: The keyword used to define a function.
- `check_login`: The identifier (name) of the function.
- `username`: The **parameter** that receives input data.
- **Indented block**: The body containing instructions to execute.
- `check_login("admin")`: The **function call** passing `"admin"` as the **argument**.

> **Important:** Defining a function does *not* execute its body. The body runs only when the function is invoked (called).

---

## 3. Functions Without Parameters

A function does not always require input arguments.

```python
def security_message():
    print("Security scan started")

security_message()
security_message()
```

Output:

```text
Security scan started
Security scan started
```

**Cybersecurity use cases:** Displaying standard banners, initializing logging systems, or printing report headers.

---

## 4. Functions with Parameters

Parameters allow functions to receive dynamic data for processing.

### Single Parameter:

```python
def check_attempts(attempts):
    if attempts > 5:
        print("Review suspicious login activity")
    else:
        print("Below the example threshold")

check_attempts(2)
check_attempts(8)
```

Output:

```text
Below the example threshold
Review suspicious login activity
```

Here, `attempts` is the **parameter** defined in the header, while `2` and `8` are the **arguments** supplied when calling the function.

### Multiple Parameters:

```python
def report_login(username, attempts):
    print(f"User: {username}")
    print(f"Failed attempts: {attempts}")

report_login("admin", 7)
```

Output:

```text
User: admin
Failed attempts: 7
```

---

## 5. The `return` Statement — Very Important

The `return` statement sends a computed result back to the code that called the function and immediately terminates function execution.

```python
def add(a, b):
    return a + b

result = add(10, 20)
print(result)
```

Output:

```text
30
```

### Critical Difference: `print()` vs. `return`

Understanding this distinction is vital for writing software and passing technical interviews:

#### 1. `print()` only displays a value to stdout:

```python
def check():
    print(100)

result = check()
print("Result is:", result)
```

Output:

```text
100
Result is: None
```

*The function prints `100`, but returns nothing explicitly. In Python, a function without an explicit `return` evaluates to `None`.*

#### 2. `return` passes data back into program memory:

```python
def check():
    return 100

result = check()
print("Result is:", result)
```

Output:

```text
Result is: 100
```

*The returned value can be stored in a variable, evaluated in an `if` condition, or passed into another function.*

### Cybersecurity Example:

```python
def is_suspicious(attempts):
    return attempts >= 5

result = is_suspicious(8)

if result:
    print("Investigate this account")
else:
    print("No alert from this rule")
```

Output:

```text
Investigate this account
```

> **Rule of Thumb:** Use `print()` when presenting output to human operators. Use `return` when passing data to other functions, validation checks, or security automation pipelines.

---

## 6. Functions with Multiple Return Values

Python allows functions to return multiple values simultaneously, which are packed and returned as a **tuple**.

```python
def analyze_attempts(attempts):
    total = len(attempts)
    highest = max(attempts)
    return total, highest

count, highest = analyze_attempts([2, 7, 3, 9, 1])

print("Number of records:", count)
print("Highest count:", highest)
```

Output:

```text
Number of records: 5
Highest count: 9
```

*Ideal for returning multiple telemetry metrics (e.g., minimum, maximum, average latency, or alert count) in a single pass.*

---

## 7. Default Parameters

A default parameter specifies a fallback value used whenever the caller does not supply that argument.

```python
def check_attempts(attempts, threshold=5):
    return attempts >= threshold

print(check_attempts(3))      # False: uses default threshold of 5
print(check_attempts(8))      # True: uses default threshold of 5
print(check_attempts(3, 2))   # True: overrides default threshold with 2
```

Output:

```text
False
True
True
```

> **Syntax Rule:** Parameters with default values must always appear *after* parameters without default values in the function definition (e.g., `def func(a, b=10):`, never `def func(a=10, b):`).

---

## 8. Positional and Keyword Arguments

### Positional Arguments
Arguments are mapped to parameters based on their order in the function call:

```python
def report(username, attempts):
    print(username, attempts)

report("admin", 6)
```

Output:

```text
admin 6
```

### Keyword Arguments
Arguments are mapped explicitly by parameter name, regardless of order:

```python
report(attempts=6, username="admin")
```

Output:

```text
admin 6
```

*Keyword arguments improve code clarity and reduce parameter-ordering bugs in functions with many configurable flags.*

---

## 9. Variable-Length Arguments: `*args` and `**kwargs`

Useful when a security tool needs to accept an arbitrary number of inputs.

### `*args` (Arbitrary Positional Arguments)
Collects extra positional arguments into a **tuple**:

```python
def calculate_total(*numbers):
    return sum(numbers)

print(calculate_total(2, 3))
print(calculate_total(2, 3, 4, 5))
```

Output:

```text
5
14
```

### `**kwargs` (Arbitrary Keyword Arguments)
Collects extra named arguments into a **dictionary**:

```python
def show_event(**details):
    print(details)

show_event(
    username="admin",
    attempts=7,
    status="review"
)
```

Output:

```text
{'username': 'admin', 'attempts': 7, 'status': 'review'}
```

*Security note: In production tools, always sanitize and validate `**kwargs` against an allowed whitelist of fields.*

---

## 10. Local and Global Scope

### Local Scope
Variables defined inside a function exist only during the execution of that function:

```python
def check_user():
    username = "admin"
    print(username)

check_user()
```

Attempting to access `username` outside raises a `NameError`:

```python
def check_user():
    username = "admin"

check_user()
print(username)  # NameError: name 'username' is not defined
```

### Global Scope
Variables defined at the module root can be read inside functions:

```python
threshold = 5

def check_attempts(attempts):
    return attempts >= threshold

print(check_attempts(7))
```

Output:

```text
True
```

> **Best Practice:** Avoid relying on global state or mutating globals with the `global` keyword. Pass configuration variables explicitly as parameters to ensure functions remain pure, isolated, and easy to unit test.

---

## 11. The `pass` Statement Inside Functions

When designing security architectures, you can stub out unwritten functions with `pass`:

```python
def analyze_log():
    pass  # Placeholder: Implementation scheduled for sprint 2
```

`pass` satisfies the interpreter while keeping the script syntactically valid.

---

## 12. Docstrings

A docstring documents what a function does, its parameters, and its return values. It is written using triple quotes (`"""..."""`):

```python
def count_failed_logins(logs):
    """Count log entries containing a failed-login message."""
    count = 0
    for log in logs:
        if "failed login" in log.lower():
            count += 1
    return count
```

Inspect docstrings at runtime:

```python
print(count_failed_logins.__doc__)
```

Output:

```text
Count log entries containing a failed-login message.
```

---

## 13. Calling One Function from Another (Modularity)

Complex security tools should be broken down into small, single-responsibility functions that compose together:

```python
def count_errors(logs):
    count = 0
    for log in logs:
        if "ERROR" in log:
            count += 1
    return count

def generate_report(logs):
    total = len(logs)
    errors = count_errors(logs)
    print(f"Total log entries: {total}")
    print(f"Error entries: {errors}")

logs = [
    "INFO: Started",
    "ERROR: Failed login",
    "ERROR: Invalid token"
]

generate_report(logs)
```

Output:

```text
Total log entries: 3
Error entries: 2
```

---

## 14. Type Hints

Type hints declare expected parameter and return types. They enhance code maintainability, IDE autocompletion, and static analysis via tools like `mypy`:

```python
def is_suspicious(attempts: int) -> bool:
    return attempts >= 5

print(is_suspicious(8))
```

> **Note:** Python is dynamically typed; type hints are not enforced at runtime by default, but they are a standard industry best practice.

---

## 15. Lambda Functions

A `lambda` creates an anonymous, inline function consisting of a single expression:

```python
square = lambda number: number * number
print(square(5))
```

Output:

```text
25
```

Equivalent regular function:

```python
def square(number):
    return number * number
```

*Lambdas are often passed as custom key extractors in `sorted()`, `filter()`, or `map()`.*

---

## 16. Complete Mini Project — Security Log Analyzer

This project demonstrates functions, default arguments, iteration, condition matching, string methods, and return statements working in harmony:

```python
def count_failed_logins(logs):
    """Count failed-login entries."""
    count = 0
    for log in logs:
        if "failed login" in log.lower():
            count += 1
    return count

def analyze_logs(logs, threshold=3):
    """Summarize logs and flag a high event count."""
    total = len(logs)
    failed = count_failed_logins(logs)
    
    print(f"Total log entries: {total}")
    print(f"Failed-login entries: {failed}")
    
    if failed >= threshold:
        print("Alert: Review repeated failed-login activity")
    else:
        print("Failed-login count is below the threshold")

sample_logs = [
    "INFO: Server started",
    "ERROR: Failed login for admin",
    "WARNING: Retry detected",
    "ERROR: Failed login for guest",
    "ERROR: FAILED LOGIN for admin"
]

analyze_logs(sample_logs)
```

Output:

```text
Total log entries: 5
Failed-login entries: 3
Alert: Review repeated failed-login activity
```

---

## 17. Common Function Interview Questions

| Question | Answer |
| --- | --- |
| **What is a function?** | A reusable, named block of code that performs a specific task. |
| **Which keyword defines a function?** | `def` |
| **What is a parameter?** | A variable listed in the function definition header. |
| **What is an argument?** | The concrete value passed into a function when calling it. |
| **What does `return` do?** | Passes a computed value back to the caller and exits the function. |
| **What happens if a function has no `return` statement?** | It returns `None` implicitly. |
| **What is a default parameter?** | A parameter assigned a fallback value used when no argument is provided. |
| **What is the difference between local and global variables?** | Local variables exist only within the function's scope; global variables are accessible throughout the module. |
| **What is `*args`?** | Syntax allowing a function to accept arbitrary positional arguments collected as a tuple. |
| **What is `**kwargs`?** | Syntax allowing a function to accept arbitrary keyword arguments collected as a dictionary. |
| **What is a lambda function?** | A concise, single-expression anonymous function. |
| **What is recursion?** | A programming pattern where a function calls itself to solve smaller subproblems. |

---

## 18. Practice Exercises & Solutions

### Exercise 1: Basic Function
**Task:** Write a function called `greet_user(username)` that prints a greeting containing the username.

```python
def greet_user(username):
    print(f"Welcome to Security Ops, {username}!")

greet_user("analyst1")
```

Output:

```text
Welcome to Security Ops, analyst1!
```

---

### Exercise 2: Return a Boolean Value
**Task:** Write a function `is_valid_port(port)` that returns `True` when the port is between 1 and 65535 (inclusive), and `False` otherwise.

```python
def is_valid_port(port: int) -> bool:
    return 1 <= port <= 65535

print(is_valid_port(443))    # True
print(is_valid_port(70000))  # False
print(is_valid_port(0))      # False
```

---

### Exercise 3: Count Events
**Task:** Write a function `count_errors(logs)` that returns the number of entries containing `"ERROR"`, ignoring case.

```python
def count_errors(logs):
    error_count = 0
    for log in logs:
        if "error" in log.lower():
            error_count += 1
    return error_count

log_data = [
    "INFO: User logged in",
    "error: database timeout",
    "ERROR: Invalid token signature",
    "WARN: High CPU utilization"
]

print("Error count:", count_errors(log_data))
```

Output:

```text
Error count: 2
```

---

### Exercise 4: Default Parameter
**Task:** Write a function `is_suspicious(attempts, threshold=5)` that returns a Boolean indicating if `attempts >= threshold`.

```python
def is_suspicious(attempts, threshold=5):
    return attempts >= threshold

print(is_suspicious(3))      # False
print(is_suspicious(6))      # True
print(is_suspicious(3, 2))   # True (custom threshold)
```

---

### Exercise 5: Combine Modular Functions
**Task:** Write a function that counts failed logins, then another function that prints a formatted incident report using the returned count.

```python
def get_failed_count(logs):
    return sum(1 for log in logs if "failed login" in log.lower())

def audit_report(system_name, logs):
    failed = get_failed_count(logs)
    print(f"=== Security Audit: {system_name} ===")
    print(f"Total Events Inspected: {len(logs)}")
    print(f"Failed Login Incidents: {failed}")
    if failed > 0:
        print("[STATUS] Escalation required for incident response")
    else:
        print("[STATUS] No failed logins detected")

test_logs = [
    "Failed login from IP 192.0.2.1",
    "Session started",
    "Failed login from IP 198.51.100.4"
]

audit_report("Auth-Server-01", test_logs)
```

Output:

```text
=== Security Audit: Auth-Server-01 ===
Total Events Inspected: 3
Failed Login Incidents: 2
[STATUS] Escalation required for incident response
```

---

## 19. What You Should Learn Next

After functions, follow this structured roadmap:

1. **Lists, Tuples, Sets, and Dictionaries in Depth:** Nested data structures, list comprehensions, and dictionary mapping.
2. **Exception Handling:** `try`, `except`, `else`, and `finally` to build resilient security tools.
3. **File Handling:** Safely reading/writing syslog files, incident report CSVs, and JSON configurations.
4. **Modules and Packages:** Organizing your code across files and leveraging standard libraries like `os`, `sys`, and `hashlib`.
5. **Object-Oriented Programming (OOP):** Creating custom classes for `SecurityEvent`, `PortScanner`, or `IncidentReport`.
6. **Regular Expressions (`re`):** Extracting IP addresses, hashes, CVE numbers, and email headers.
7. **Cybersecurity Projects:** Constructing custom SIEM parsers, brute-force detectors, and File Integrity Monitors (FIM).

> **Core Takeaway:** Master the relationship between **parameters**, **arguments**, **`return`**, and **`print()`**. Once this foundation is solid, designing clean and modular cybersecurity automation scripts becomes natural.


