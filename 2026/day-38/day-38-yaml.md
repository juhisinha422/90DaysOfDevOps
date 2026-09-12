# Day 38 – YAML Basics

## Task 1: Key-Value Pairs

### Created the person.yaml that describes the following points:

name
role
experience_years
learning (a boolean)

Verified: Ran cat person.yaml and confirmed the YAML is clean with no tabs.

```bash

───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: person.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ name: Abusufiyan
   2   │ role: DevOps_Engineer
   3   │ experience_years: 3
   4   │ learning: True
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

```

## Task 2: Lists

### Added to person.yaml:

tools — a list of 5 DevOps tools you know or are learning
hobbies — a list using the inline format [item1, item2]

```bash

───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: person.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ name: Abusufiyan
   2   │ role: DevOps_Engineer
   3   │ experience_years: 3
   4   │ learning: true
   5   │
   6   │ tools:
   7   │   - CI/CD
   8   │   - Terraform
   9   │   - Docker
  10   │   - K8s
  11   │   - Ansible
  12   │
  13   │ hobbies: [Reading, Debugging]
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

```

## 📝 YAML Lists

There are two ways to write a list in YAML:

### 📝 Two Ways to Write a List in YAML

YAML provides two common ways to write lists.

#### 1. Block Format

Each item is written on a new line using `-`:

```yaml
tools:
  - Docker
  - Terraform
  - Ansible
```

#### 2. Inline (Flow) Format

Items can also be written inside square brackets `[ ]`:

```yaml
tools: [Docker, Terraform, Ansible]
```

**Note:** Both formats represent the same type of YAML list.

### ✅ YAML Verification

Verified the YAML file using:

```bash
cat person.yaml
```

## Task 3: Nested Objects

### Created server.yaml that describes a server:
```bash
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: server.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ # Details of the Server
   2   │ server:
   3   │   name: Test_Server
   4   │   ip: 172.10.**.**
   5   │   port: 5000
   6   │
   7   │ # Database server
   8   │ database:
   9   │   host: DB_Server
  10   │   name: DB_Server
  11   │   credentials:
  12   │     user: Test
  13   │     password: Test@123
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```
### 🔍 YAML Validation – Tabs vs Spaces

**Verify:** Tried adding a tab instead of spaces for indentation.

**Result:** YAML does not allow tabs for indentation. When the file is validated, it produces an error such as:

```text
found character '\t' that cannot start any token

```

## Task 4: Multi-line Strings

In server.yaml, add a startup_script field using:

The | block style (preserves newlines)
The > fold style (folds into one line)

```bash

───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: server.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ # Details of the Server
   2   │ server:
   3   │   name: Test_Server
   4   │   ip: 172.10.**.**
   5   │   port: 5000
   6   │
   7   │   # | preserves newlines
   8   │   startup_script: |
   9   │     #!/bin/bash
  10   │     echo "Starting server"
  11   │     systemctl start nginx
  12   │     echo "Server started"
  13   │
  14   │ # Database server
  15   │ database:
  16   │   host: DB_Server
  17   │   name: DB_Server
  18   │   credentials:
  19   │     user: Test
  20   │     password: Test@123
  21   │
  22   │   # > folds multiple lines into one line
  23   │   startup_script: >
  24   │     echo "Connecting to database"
  25   │     echo "Checking database status"
  26   │     echo "Database is ready"
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

```

## 📝 YAML Block Styles – `|` vs `>`

YAML provides two block styles for handling multi-line values:

### `|` — Literal Block Style

- Preserves **new lines**.
- Useful when line breaks are important.
- Commonly used for **shell scripts, configuration files, and multi-line text**.

Example:

```yaml
startup_script: |
  echo "Starting server"
  echo "Checking services"
  echo "Server started"
```

Each line remains on a separate line.

### `>` — Folded Block Style

- **Folds multiple lines into one line**.
- Useful for **long sentences or text** where line breaks are not important.

Example:

```yaml
description: >
  This is a long sentence
  written across multiple
  lines in YAML.
```

The result is treated approximately as:

```text
This is a long sentence written across multiple lines in YAML.
```

### 💡 Easy Way to Remember

```text
|  → Keep new lines
>  → Combine / fold lines
```

> ⚠️ **Note:** `|` preserves line breaks, but it does not automatically run commands at intervals. It only controls how the multi-line value is represented.

## Task 5: Validate Your YAML

### Install yamllint or use an online validator

```bash
ubuntu@ip-172-31-35-103:~$ yamllint --version
yamllint 1.37.1
ubuntu@ip-172-31-35-103:~$
```
### Validate both your YAML files

```bash
ubuntu@ip-172-31-35-103:~$ yamllint --version
yamllint 1.37.1
ubuntu@ip-172-31-35-103:~$ ls
person.yaml  server.yaml
ubuntu@ip-172-31-35-103:~$ yamllint -c person.yaml server.yaml
ubuntu@ip-172-31-35-103:~$
```

### Intentionally break the indentation — what error do you get?
```bash
ubuntu@ip-172-31-35-103:~$ yamllint server.yaml
server.yaml
  2:1       warning  missing document start "---"  (document-start)
  8:3       error    syntax error: expected <block end>, but found '<block mapping start>' (syntax)

ubuntu@ip-172-31-35-103:~$
```
### Fix it and validate again

```bash
ubuntu@ip-172-31-35-103:~$ yamllint server.yaml
server.yaml
  2:1       warning  missing document start "---"  (document-start)
  8:3       error    syntax error: expected <block end>, but found '<block mapping start>' (syntax)

ubuntu@ip-172-31-35-103:~$ vim server.yaml
ubuntu@ip-172-31-35-103:~$
ubuntu@ip-172-31-35-103:~$ yamllint server.yaml
server.yaml
  2:1       warning  missing document start "---"  (document-start)

ubuntu@ip-172-31-35-103:~$ vim server.yaml
ubuntu@ip-172-31-35-103:~$
ubuntu@ip-172-31-35-103:~$ yamllint server.yaml
ubuntu@ip-172-31-35-103:~$
```

### Task 6: Spot the Difference

Difference: Block 2 has incorrect indentation. YAML uses indentation to define structure. Both list items under tools must have the same indentation level. - docker and - kubernetes should be aligned.
