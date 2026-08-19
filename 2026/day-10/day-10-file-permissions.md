# Day 10 Challenge

## Files Created
- `devops.txt` (Empty file created using `touch`)
- `notes.txt` (File with content created using `echo`)
- `script.sh` (Bash script created using `vim`)
- `project/` (Directory created using `mkdir`)

<img width="2940" height="1912" alt="Image" src="https://github.com/user-attachments/assets/7e82c374-c6eb-4d30-a839-b023eb177b55" />
<img width="2940" height="1912" alt="Image" src="https://github.com/user-attachments/assets/371be455-4d9f-455d-9b78-78e49b25560b" />

## Permission Changes

**Initial Permissions Analysis:**
- Default files were created with `rw-rw-r--` (Read/Write for Owner & Group, Read for Others).
<img width="2936" height="542" alt="Image" src="https://github.com/user-attachments/assets/cf45b988-0159-43ef-925f-1f4d22af7dfe" />

**Modifications Made:**
- **`script.sh`:** Before `-rw-rw-r--` | After `-rwxrwxr-x` (Made executable)
- **`devops.txt`:** Before `-rw-rw-r--` | After `-r--r--r--` (Made read-only for all)
- **`notes.txt`:** Before `-rw-rw-r--` | After `-rw-r-----` (Set to 640 numeric)
- **`project/`:** Set directly to `drwxr-xr-x` (Set to 755 numeric)

<img width="2250" height="1270" alt="Image" src="https://github.com/user-attachments/assets/5ac3ae15-d9c2-4f10-9c78-33486acbcec1" />

## Commands Used
```bash
# Creating files
mkdir day-10
touch devops.txt
echo "Learning File Permissions on Day 10" > notes.txt
vim script.sh

# Reading files
cat notes.txt
vim -R script.sh
head -n 5 /etc/passwd
tail -n 5 /etc/passwd

# Modifying permissions
chmod +x script.sh
chmod a-w devops.txt
chmod 640 notes.txt
mkdir project
chmod 755 project
```

## What I Learned
1. **The 4-2-1 Numeric Rule:** Permissions are simple math! Read is 4, Write is 2, and Execute is 1. Combining them (like 6 for rw, or 7 for rwx) gives you exact control over access.
2. **Execution is Explicit:** Linux does not allow any file to run as a script automatically. You must explicitly grant execute (`+x`) permissions, which is a massive security advantage.
3. **Intentional Errors are Guides:** Trying to write to a read-only file or executing a non-executable file triggers `Permission denied`. Understanding these errors helps in debugging server incidents faster.

<img width="2754" height="314" alt="Image" src="https://github.com/user-attachments/assets/38abf345-9ab6-4f34-aa8d-59f334acc3c3" />
