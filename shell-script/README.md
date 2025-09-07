Perfect ⚡


# 🔧 Shell Script Automation Challenges

This repository contains **10 shell scripting automation challenges** with solutions.
Each challenge focuses on a real-world automation task, helping you practice shell scripting concepts such as **file handling, process management, monitoring, and system administration**.

---

## 📂 Challenges

### 1. File Backup Script

👉 Write a script that automatically creates a backup of a given folder and saves it with the current date in the filename.

**Solution:**

```bash
#!/bin/bash
src="/home/user/data"
dest="/home/user/backup/data_$(date +%F).tar.gz"
tar -czf $dest $src
echo "Backup created at $dest"
```

---

### 2. User Account Creation

👉 Write a script to create multiple user accounts from a text file containing usernames.

**Solution:**

```bash
#!/bin/bash
file="users.txt"
while read user; do
    sudo useradd $user
    echo "User $user created"
done < $file
```

---

### 3. Log File Cleaner

👉 Write a script that deletes log files older than 7 days from `/var/log`.

**Solution:**

```bash
#!/bin/bash
find /var/log -type f -name "*.log" -mtime +7 -exec rm -f {} \;
echo "Old log files deleted."
```

---

### 4. Disk Space Monitor

👉 Write a script to check disk usage and send a warning if usage exceeds 80%.

**Solution:**

```bash
#!/bin/bash
usage=$(df / | grep / | awk '{print $5}' | sed 's/%//')
if [ $usage -gt 80 ]; then
    echo "Warning: Disk usage is above 80% ($usage%)"
fi
```

---

### 5. Process Monitor

👉 Write a script to check if a process (e.g., `nginx` or `mysql`) is running, and restart it if it’s not.

**Solution:**

```bash
#!/bin/bash
service="nginx"
if ! pgrep -x "$service" > /dev/null; then
    echo "$service is not running, restarting..."
    sudo systemctl restart $service
else
    echo "$service is running."
fi
```

---

### 6. System Health Report

👉 Write a script that generates a report showing CPU, memory, and disk usage, then saves it in a file.

**Solution:**

```bash
#!/bin/bash
report="system_report_$(date +%F).txt"
echo "System Health Report - $(date)" > $report
echo "---------------------------------" >> $report
echo "CPU Load:" >> $report
uptime >> $report
echo "" >> $report
echo "Memory Usage:" >> $report
free -h >> $report
echo "" >> $report
echo "Disk Usage:" >> $report
df -h >> $report
echo "Report saved to $report"
```

---

### 7. Automated File Rename

👉 Write a script to rename all `.txt` files in a folder by adding today’s date at the end of the filename.

**Solution:**

```bash
#!/bin/bash
folder="/home/user/docs"
for file in $folder/*.txt; do
    mv "$file" "${file%.txt}_$(date +%F).txt"
done
echo "All files renamed."
```

---

### 8. Database Backup

👉 Write a script that takes a backup of a MySQL database and compresses it into a `.tar.gz` file.

**Solution:**

```bash
#!/bin/bash
db="mydb"
user="root"
pass="password"
backup="db_backup_$(date +%F).sql"
mysqldump -u $user -p$pass $db > $backup
tar -czf $backup.tar.gz $backup
rm $backup
echo "Database backup created: $backup.tar.gz"
```

---

### 9. Email Alert System

👉 Write a script that sends an email alert if a specific service goes down (e.g., Apache, MySQL).

**Solution:**

```bash
#!/bin/bash
service="apache2"
if ! systemctl is-active --quiet $service; then
    echo "$service is down on $(hostname)" | mail -s "$service Alert" admin@example.com
fi
```

---

### 10. Auto Cleanup Script

👉 Write a script that finds and deletes all temporary files (`*.tmp`, `*.bak`, `*.swp`) from a directory.

**Solution:**

```bash
#!/bin/bash
dir="/home/user/temp"
find $dir -type f \( -name "*.tmp" -o -name "*.bak" -o -name "*.swp" \) -exec rm -f {} \;
echo "Temporary files deleted from $dir"
```

---

## ▶️ How to Run

1. Clone or download this project.
2. Make scripts executable:

   ```bash
   chmod +x script.sh
   ```
3. Run the script:

   ```bash
   ./script.sh
   ```

---

## 🎯 Learning Outcomes

By solving these challenges, you will learn to:

* Automate backups & cleanups
* Manage users and processes
* Monitor system health
* Work with files and directories
* Send alerts and notifications

---



---

