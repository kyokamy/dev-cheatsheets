# 🐧 The Ultimate Bash & Linux Cheatsheet

### *Because the terminal is your best friend, and you should know how to talk to it*

---

## 🎬 Act I: Getting Started (a.k.a. "Hello, Terminal")

### Basic Navigation

```bash
# Current directory
pwd

# List files
ls
ls -l              # Long format
ls -la             # All files (including hidden)
ls -lh             # Human-readable sizes
ls -lt             # Sorted by time
ls -ltr            # Reverse time order

# Change directory
cd /path/to/dir
cd ~               # Home directory
cd -               # Previous directory
cd ..              # Parent directory
cd ../..           # Two levels up

# Create directory
mkdir dirname
mkdir -p path/to/dir    # Create parent directories

# Remove directory
rmdir dirname      # Empty directory only
rm -r dirname      # Recursive (careful!)
rm -rf dirname     # Force recursive (VERY careful!)

# Create file
touch filename
touch file1 file2 file3

# Remove file
rm filename
rm -f filename     # Force (no confirmation)
rm -i filename     # Interactive (confirmation)
```

---

## 📦 Act II: File Operations (The Basics)

### Viewing Files

```bash
# View entire file
cat filename
cat file1 file2    # Concatenate files

# View with pagination
less filename
more filename

# View first lines
head filename
head -n 20 filename    # First 20 lines

# View last lines
tail filename
tail -n 20 filename     # Last 20 lines
tail -f filename        # Follow (watch for changes)

# Count lines, words, characters
wc filename
wc -l filename          # Lines only
wc -w filename          # Words only
wc -c filename          # Characters only
```

### Copying & Moving

```bash
# Copy file
cp source dest
cp file1 file2
cp -r dir1 dir2         # Recursive (directory)

# Move/rename
mv source dest
mv oldname newname
mv file1 dir/            # Move to directory

# Create symbolic link
ln -s target linkname
ln -s /path/to/file symlink
```

### Finding Files

```bash
# Find files
find . -name "*.txt"
find /path -name "filename"
find . -type f -name "*.js"
find . -type d -name "node_modules"
find . -size +100M       # Files larger than 100MB
find . -mtime -7         # Modified in last 7 days

# Locate (faster, uses database)
locate filename
updatedb                 # Update locate database

# Which (find executable)
which command
which python
which node

# Whereis (find binary, source, manual)
whereis command
whereis python
```

---

## 🔍 Act III: Text Processing (The Power Tools)

### grep (Search in Files)

```bash
# Basic search
grep "pattern" file
grep "error" log.txt

# Case insensitive
grep -i "pattern" file

# Recursive
grep -r "pattern" .
grep -r "error" /var/log

# Show line numbers
grep -n "pattern" file

# Invert match (show non-matching)
grep -v "pattern" file

# Count matches
grep -c "pattern" file

# Show context
grep -A 3 "pattern" file    # After context
grep -B 3 "pattern" file    # Before context
grep -C 3 "pattern" file    # Both

# Regular expressions
grep "^start" file           # Lines starting with "start"
grep "end$" file            # Lines ending with "end"
grep -E "pattern1|pattern2" file  # Extended regex
```

### sed (Stream Editor)

```bash
# Replace first occurrence per line
sed 's/old/new/' file

# Replace all occurrences
sed 's/old/new/g' file

# Replace in place (GNU sed)
sed -i 's/old/new/g' file

# Delete lines
sed '5d' file               # Delete line 5
sed '/pattern/d' file        # Delete matching lines

# Print specific lines
sed -n '5p' file            # Print line 5
sed -n '10,20p' file        # Print lines 10-20
```

### awk (Text Processing)

```bash
# Print columns
awk '{print $1}' file       # First column
awk '{print $1, $3}' file   # First and third columns

# Print lines matching pattern
awk '/pattern/ {print}' file

# Field separator
awk -F: '{print $1}' /etc/passwd    # Colon-separated

# Calculations
awk '{sum+=$1} END {print sum}' file
awk '{print $1, $2, $1+$2}' file
```

### cut (Extract Columns)

```bash
# Cut by delimiter
cut -d: -f1 /etc/passwd     # First field, colon-delimited
cut -d, -f1,3 file.csv      # First and third fields

# Cut by character position
cut -c1-10 file             # Characters 1-10
```

---

## 🔧 Act IV: Permissions (Who Can Do What)

### Understanding Permissions

```bash
# View permissions
ls -l
# Output: -rwxr-xr-- 1 user group 1024 Dec 10 file.txt
#         ^^^^ ^^^ ^^^
#         |||  |||  |||
#         |||  |||  |++-- Others: read only
#         |||  |++-- Group: read, execute
#         |++-- Owner: read, write, execute
#         +-- File type (- = file, d = directory)

# Change permissions
chmod 755 file              # rwxr-xr-x
chmod u+x file              # Add execute for owner
chmod g-w file              # Remove write for group
chmod a+r file              # Add read for all
chmod -R 755 dir/           # Recursive

# Permission numbers
# 4 = read (r)
# 2 = write (w)
# 1 = execute (x)
# 7 = 4+2+1 (rwx)
# 6 = 4+2 (rw-)
# 5 = 4+1 (r-x)

# Change owner
chown user:group file
chown -R user:group dir/    # Recursive

# Change group
chgrp group file
```

---

## 💻 Act V: Process Management (Running Things)

### Process Commands

```bash
# List processes
ps
ps aux                    # All processes
ps aux | grep process     # Find specific process
ps -ef                    # Full format

# Process tree
pstree
pstree -p                 # With PIDs

# Top processes
top
htop                      # Better version (install separately)

# Kill process
kill PID
kill -9 PID               # Force kill
killall processname       # Kill by name

# Background/foreground
command &                 # Run in background
fg                        # Bring to foreground
bg                        # Continue in background
jobs                      # List background jobs

# No hang up (run after logout)
nohup command &
disown                    # Disown current job
```

### System Information

```bash
# System info
uname -a
hostname
uptime

# Disk usage
df -h                     # Human-readable
du -h                     # Current directory
du -sh dir/               # Summary of directory
du -h --max-depth=1       # One level deep

# Memory
free -h
cat /proc/meminfo

# CPU info
lscpu
cat /proc/cpuinfo

# Network
ifconfig
ip addr                   # Modern alternative
netstat -tulpn            # Network connections
ss -tulpn                 # Modern alternative
```

---

## 📊 Act VI: Redirection & Pipes (Data Flow)

### Redirection

```bash
# Output to file
command > file            # Overwrite
command >> file           # Append

# Input from file
command < file

# Error redirection
command 2> error.log      # Stderr to file
command 2>&1              # Stderr to stdout
command > file 2>&1       # Both to file
command &> file           # Both to file (bash)

# Discard output
command > /dev/null
command 2> /dev/null
command > /dev/null 2>&1  # Discard everything
```

### Pipes

```bash
# Pipe output to next command
command1 | command2
ls -l | grep "\.txt"
ps aux | grep python

# Multiple pipes
command1 | command2 | command3
cat file | grep "error" | wc -l

# Tee (split output)
command | tee file        # Output to both stdout and file
command | tee -a file     # Append mode
```

---

## 🔐 Act VII: Compression & Archives (Pack It Up)

### tar (Archive)

```bash
# Create archive
tar -czf archive.tar.gz dir/
tar -czf archive.tgz dir/
tar -cjf archive.tar.bz2 dir/

# Extract archive
tar -xzf archive.tar.gz
tar -xzf archive.tar.gz -C /destination

# List archive contents
tar -tzf archive.tar.gz

# Extract specific files
tar -xzf archive.tar.gz file1 file2
```

### zip/unzip

```bash
# Create zip
zip -r archive.zip dir/
zip archive.zip file1 file2

# Extract zip
unzip archive.zip
unzip archive.zip -d /destination

# List zip contents
unzip -l archive.zip
```

### gzip/gunzip

```bash
# Compress
gzip file
gzip -k file              # Keep original

# Decompress
gunzip file.gz
gzip -d file.gz
```

---

## 🌐 Act VIII: Network Commands (Connectivity)

### Network Tools

```bash
# Ping
ping hostname
ping -c 4 hostname        # 4 packets
ping -i 2 hostname         # Interval of 2 seconds

# Check connectivity
curl http://example.com
wget http://example.com/file

# DNS lookup
nslookup domain.com
dig domain.com
host domain.com

# Network configuration
ifconfig
ip addr show
ip route show

# Port scanning
nc -zv hostname 80         # Check if port is open
telnet hostname 80

# Download files
wget URL
wget -O filename URL       # Save as filename
wget -c URL                # Continue partial download

# HTTP requests
curl URL
curl -O URL                # Save file
curl -L URL                # Follow redirects
curl -H "Header: value" URL
curl -X POST -d "data" URL
```

---

## 🛠️ Act IX: Useful Commands & Tricks

### Date & Time

```bash
# Current date/time
date
date +"%Y-%m-%d"
date +"%H:%M:%S"

# Set date (requires root)
date -s "2024-12-10 10:30:00"

# Timestamp
date +%s                   # Unix timestamp
```

### History

```bash
# Command history
history
history | grep command
!n                         # Execute command number n
!!                          # Last command
!string                     # Last command starting with string

# History configuration
export HISTSIZE=10000
export HISTFILESIZE=20000
export HISTCONTROL=ignoredups
```

### Aliases

```bash
# Create alias
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias ..='cd ..'
alias ...='cd ../..'

# List aliases
alias

# Remove alias
unalias ll

# Persistent aliases (add to ~/.bashrc)
echo "alias ll='ls -alF'" >> ~/.bashrc
source ~/.bashrc
```

### Environment Variables

```bash
# Set variable
export VAR=value
export PATH=$PATH:/new/path

# View variable
echo $VAR
env                        # All environment variables

# Unset variable
unset VAR
```

---

## 🎯 Act X: Advanced Bash Features

### Variables & Substitution

```bash
# Variable assignment
VAR="value"
echo $VAR
echo ${VAR}

# Default values
${VAR:-default}            # Use default if VAR is unset
${VAR:=default}            # Set to default if unset
${VAR:+value}              # Use value if VAR is set
${VAR:?error}             # Error if VAR is unset

# String operations
${#VAR}                    # Length
${VAR:start:length}        # Substring
${VAR#pattern}              # Remove shortest prefix
${VAR##pattern}            # Remove longest prefix
${VAR%pattern}             # Remove shortest suffix
${VAR%%pattern}            # Remove longest suffix
```

### Loops & Conditionals

```bash
# For loop
for i in {1..10}; do
    echo $i
done

for file in *.txt; do
    echo $file
done

# While loop
while [ condition ]; do
    command
done

# If statement
if [ condition ]; then
    command
elif [ condition ]; then
    command
else
    command
fi

# Case statement
case $VAR in
    value1)
        command
        ;;
    value2)
        command
        ;;
    *)
        command
        ;;
esac
```

### Functions

```bash
# Define function
function_name() {
    local var=$1
    echo "Processing $var"
    return 0
}

# Call function
function_name argument
```

---

## 🎭 Act XI: Useful One-Liners

```bash
# Find and replace in files
find . -type f -name "*.txt" -exec sed -i 's/old/new/g' {} +

# Count files in directory
ls -1 | wc -l

# Find largest files
find . -type f -exec ls -lh {} \; | sort -k5 -hr | head -10

# Remove empty files
find . -type f -empty -delete

# Remove files older than 30 days
find . -type f -mtime +30 -delete

# Extract all zip files
for zip in *.zip; do unzip "$zip"; done

# Create backup with timestamp
cp file.txt file.txt.$(date +%Y%m%d_%H%M%S)

# Monitor log file
tail -f /var/log/syslog | grep error

# Find processes using port
lsof -i :8080
netstat -tulpn | grep :8080

# Kill all processes matching pattern
pkill -f pattern
killall processname

# Disk usage by directory
du -h --max-depth=1 | sort -hr

# Find duplicate files
find . -type f -exec md5sum {} \; | sort | uniq -d -w 32

# Generate random password
openssl rand -base64 32

# Convert line endings
dos2unix file.txt
unix2dos file.txt

# Count lines of code
find . -name '*.js' | xargs wc -l

# Find files by size
find . -type f -size +100M

# Monitor system resources
watch -n 1 'df -h; free -h'
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Kill all processes for user
pkill -u username

# Find what's using disk space
du -h --max-depth=1 / | sort -hr

# Find large files
find / -type f -size +100M 2>/dev/null

# Clear terminal
clear
reset

# Emergency exit
exit
logout
Ctrl+D

# Stop current command
Ctrl+C

# Suspend current command
Ctrl+Z

# Clear command line
Ctrl+U                    # Clear to beginning
Ctrl+K                    # Clear to end
Ctrl+W                    # Delete word before cursor

# Search command history
Ctrl+R                    # Reverse search
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| List files | `ls -la` |
| Change directory | `cd /path` |
| Current directory | `pwd` |
| Create directory | `mkdir -p dir` |
| Remove file | `rm file` |
| Remove directory | `rm -rf dir` |
| Copy | `cp source dest` |
| Move | `mv source dest` |
| Find files | `find . -name "*.txt"` |
| Search in files | `grep "pattern" file` |
| View file | `cat file` or `less file` |
| Process list | `ps aux` |
| Kill process | `kill PID` |
| Disk usage | `df -h` |
| Memory | `free -h` |
| Network | `ifconfig` or `ip addr` |
| Archive | `tar -czf archive.tar.gz dir/` |
| Extract | `tar -xzf archive.tar.gz` |
| Permissions | `chmod 755 file` |
| Owner | `chown user:group file` |

---

## 🎬 The End Credits

Remember:
- `man command` is your friend (read the manual)
- `--help` flag works for most commands
- Tab completion saves time
- `Ctrl+R` searches command history
- `sudo` is powerful (use wisely)
- Always test destructive commands first
- `rm -rf` is the nuclear option (be careful!)
- Pipes (`|`) are your best friend
- Regular expressions are worth learning

**Now go automate something awesome!** 🐧

---

*Made with ☕ and the satisfaction of a perfectly crafted one-liner*

*Last updated: When you finally remembered that command*

