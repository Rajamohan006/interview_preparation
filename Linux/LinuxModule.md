# 🐧 Linux Complete Command Reference — Interview Preparation Guide

> A structured, beginner-to-expert reference covering all essential Linux commands, concepts, and use-cases for technical interviews, system administration, and cybersecurity roles.

---

## 📑 Table of Contents

1. [Linux File System Hierarchy](#1-linux-file-system-hierarchy)
2. [Navigation & Directory Commands](#2-navigation--directory-commands)
3. [File Operations](#3-file-operations)
4. [File Viewing & Searching](#4-file-viewing--searching)
5. [File Permissions & Ownership](#5-file-permissions--ownership)
6. [User & Group Management](#6-user--group-management)
7. [Process Management](#7-process-management)
8. [System Information & Monitoring](#8-system-information--monitoring)
9. [Disk & Storage Management](#9-disk--storage-management)
10. [Networking Commands](#10-networking-commands)
11. [Package Management](#11-package-management)
12. [Archiving & Compression](#12-archiving--compression)
13. [Text Processing & Manipulation](#13-text-processing--manipulation)
14. [Shell & Environment](#14-shell--environment)
15. [System Services (systemd / init)](#15-system-services-systemd--init)
16. [SSH & Remote Access](#16-ssh--remote-access)
17. [Cron Jobs & Scheduling](#17-cron-jobs--scheduling)
18. [Log Management](#18-log-management)
19. [Security & Hardening Commands](#19-security--hardening-commands)
20. [Redirection, Pipes & Special Operators](#20-redirection-pipes--special-operators)
21. [Shell Scripting Essentials](#21-shell-scripting-essentials)
22. [Common Interview Questions — Linux](#22-common-interview-questions--linux)

---

## 1. Linux File System Hierarchy

```
/                    → Root directory (top of the tree)
├── /bin             → Essential user binaries (ls, cp, mv, cat)
├── /sbin            → System admin binaries (fdisk, ifconfig, reboot)
├── /boot            → Boot loader files (GRUB, vmlinuz, initrd)
├── /dev             → Device files (disks, terminals, null, random)
├── /etc             → System-wide configuration files
├── /home            → User home directories (/home/username)
├── /lib             → Shared libraries for /bin and /sbin
├── /lib64           → 64-bit shared libraries
├── /media           → Mount points for removable media (USB, CD)
├── /mnt             → Temporary mount points
├── /opt             → Optional/third-party software
├── /proc            → Virtual filesystem for process/kernel info
├── /root            → Home directory of root user
├── /run             → Runtime data (PIDs, sockets) since boot
├── /srv             → Data for system services (HTTP, FTP)
├── /sys             → Virtual filesystem exposing kernel/hardware info
├── /tmp             → Temporary files (cleared on reboot)
├── /usr             → Secondary hierarchy (user programs & libraries)
│   ├── /usr/bin     → Non-essential user commands
│   ├── /usr/sbin    → Non-essential system admin commands
│   ├── /usr/lib     → Libraries for /usr/bin and /usr/sbin
│   └── /usr/share   → Architecture-independent data
└── /var             → Variable data (logs, mail, databases, cache)
    ├── /var/log     → System and application log files
    ├── /var/tmp     → Temporary files preserved between reboots
    └── /var/spool   → Print/mail queues
```

---

## 2. Navigation & Directory Commands

| Command | Description | Example |
|---|---|---|
| `pwd` | Print current working directory | `pwd` |
| `cd` | Change directory | `cd /var/log` |
| `cd ~` | Go to home directory | `cd ~` |
| `cd -` | Go to previous directory | `cd -` |
| `cd ..` | Go one level up | `cd ..` |
| `cd ../..` | Go two levels up | `cd ../..` |
| `ls` | List directory contents | `ls` |
| `ls -l` | Long listing format | `ls -l` |
| `ls -a` | Show hidden files (dot files) | `ls -a` |
| `ls -la` | Long listing including hidden files | `ls -la` |
| `ls -lh` | Human-readable file sizes | `ls -lh` |
| `ls -lt` | Sort by modification time | `ls -lt` |
| `ls -lR` | Recursive listing | `ls -lR /etc` |
| `ls -S` | Sort by file size | `ls -S` |
| `tree` | Display directory tree | `tree /etc` |
| `tree -L 2` | Tree with depth limit | `tree -L 2 /var` |
| `mkdir` | Create directory | `mkdir mydir` |
| `mkdir -p` | Create nested directories | `mkdir -p a/b/c` |
| `rmdir` | Remove empty directory | `rmdir mydir` |

---

## 3. File Operations

| Command | Description | Example |
|---|---|---|
| `touch` | Create empty file or update timestamp | `touch file.txt` |
| `cp` | Copy file | `cp src.txt dst.txt` |
| `cp -r` | Copy directory recursively | `cp -r dir1/ dir2/` |
| `cp -p` | Copy preserving permissions/timestamps | `cp -p file1 file2` |
| `cp -i` | Interactive (prompt before overwrite) | `cp -i a.txt b.txt` |
| `mv` | Move or rename file/directory | `mv old.txt new.txt` |
| `mv -i` | Interactive move | `mv -i src dst` |
| `rm` | Remove file | `rm file.txt` |
| `rm -r` | Remove directory recursively | `rm -r mydir` |
| `rm -f` | Force remove (no prompt) | `rm -f file.txt` |
| `rm -rf` | Force remove recursively (use with care!) | `rm -rf /tmp/test` |
| `ln` | Create hard link | `ln file.txt hardlink.txt` |
| `ln -s` | Create symbolic (soft) link | `ln -s /etc/hosts hosts_link` |
| `readlink` | Read symbolic link target | `readlink -f link_name` |
| `stat` | Display detailed file metadata | `stat file.txt` |
| `file` | Determine file type | `file binary_file` |
| `wc` | Word/line/char count | `wc -l file.txt` |
| `wc -w` | Count words | `wc -w file.txt` |
| `wc -c` | Count bytes | `wc -c file.txt` |
| `split` | Split large files | `split -l 100 bigfile.txt part_` |
| `truncate` | Shrink or extend file size | `truncate -s 0 file.txt` |
| `shred` | Securely delete file (overwrite) | `shred -u secret.txt` |

---

## 4. File Viewing & Searching

### Viewing Files

| Command | Description | Example |
|---|---|---|
| `cat` | Print file content to stdout | `cat /etc/passwd` |
| `cat -n` | Print with line numbers | `cat -n file.txt` |
| `tac` | Print file in reverse (bottom to top) | `tac file.txt` |
| `less` | Page-through file (scrollable) | `less /var/log/syslog` |
| `more` | Basic pager | `more file.txt` |
| `head` | View first N lines (default 10) | `head file.txt` |
| `head -n 20` | View first 20 lines | `head -n 20 file.txt` |
| `tail` | View last N lines (default 10) | `tail file.txt` |
| `tail -n 50` | View last 50 lines | `tail -n 50 file.txt` |
| `tail -f` | Follow file in real-time (live logs) | `tail -f /var/log/syslog` |
| `tail -F` | Follow with file re-open on rotation | `tail -F /var/log/app.log` |
| `xxd` | Hex dump of a file | `xxd file.bin` |
| `od` | Octal dump | `od -c file.bin` |
| `strings` | Extract printable strings from binary | `strings /usr/bin/ls` |

### Searching Files

| Command | Description | Example |
|---|---|---|
| `grep` | Search text using patterns | `grep "error" /var/log/syslog` |
| `grep -i` | Case-insensitive search | `grep -i "error" file.log` |
| `grep -r` | Recursive search in directory | `grep -r "TODO" ./src/` |
| `grep -n` | Show line numbers | `grep -n "fail" file.txt` |
| `grep -v` | Invert match (exclude lines) | `grep -v "debug" file.log` |
| `grep -c` | Count matching lines | `grep -c "error" file.log` |
| `grep -l` | List files with matches | `grep -rl "password" /etc/` |
| `grep -E` | Extended regex (ERE) | `grep -E "err|warn" file.log` |
| `grep -w` | Match whole word only | `grep -w "root" /etc/passwd` |
| `grep -A 3` | Print 3 lines after match | `grep -A 3 "ERROR" app.log` |
| `grep -B 3` | Print 3 lines before match | `grep -B 3 "ERROR" app.log` |
| `grep -C 3` | Print 3 lines around match | `grep -C 3 "ERROR" app.log` |
| `egrep` | Same as `grep -E` | `egrep "err|warn" file.log` |
| `fgrep` | Fixed string search (no regex) | `fgrep "127.0.0.1" hosts` |
| `find` | Search files/directories | `find / -name "*.conf"` |
| `find -type f` | Find only files | `find /etc -type f` |
| `find -type d` | Find only directories | `find /var -type d` |
| `find -name` | Find by name | `find / -name "passwd"` |
| `find -iname` | Case-insensitive name | `find / -iname "*.log"` |
| `find -size` | Find by size | `find / -size +100M` |
| `find -mtime` | Find by modification time | `find / -mtime -7` |
| `find -perm` | Find by permissions | `find / -perm 777` |
| `find -user` | Find by owner | `find / -user root` |
| `find -exec` | Execute command on results | `find /tmp -type f -exec rm {} \;` |
| `locate` | Fast file search using database | `locate nginx.conf` |
| `updatedb` | Update locate database | `sudo updatedb` |
| `which` | Find command location in PATH | `which python3` |
| `whereis` | Locate binary, source, man pages | `whereis nginx` |
| `type` | Show command type (alias, builtin) | `type ls` |

---

## 5. File Permissions & Ownership

### Understanding Permissions

```
Permission String Format:
  -rwxr-xr--
  │├┤├─┤├─┤
  ││ │  │  └── Others (r--)  → Read only
  ││ │  └───── Group  (r-x)  → Read + Execute
  ││ └──────── User   (rwx)  → Read + Write + Execute
  │└────────── File type: - (file), d (dir), l (symlink), b (block), c (char)

Permission Values:
  r = 4 (read)
  w = 2 (write)
  x = 1 (execute)
  - = 0 (no permission)

Examples:
  rwx = 4+2+1 = 7
  rw- = 4+2+0 = 6
  r-x = 4+0+1 = 5
  r-- = 4+0+0 = 4
  --- = 0+0+0 = 0
```

### chmod — Change Permissions

| Command | Description |
|---|---|
| `chmod 755 file` | rwxr-xr-x (owner full, group/others r+x) |
| `chmod 644 file` | rw-r--r-- (owner rw, group/others r) |
| `chmod 600 file` | rw------- (owner rw only, private) |
| `chmod 777 file` | rwxrwxrwx (full access all — avoid!) |
| `chmod 400 file` | r-------- (owner read-only) |
| `chmod 700 file` | rwx------ (owner full, no others) |
| `chmod u+x file` | Add execute for user/owner |
| `chmod g-w file` | Remove write from group |
| `chmod o=r file` | Set others to read only |
| `chmod a+x file` | Add execute for all (user+group+others) |
| `chmod -R 755 dir` | Apply recursively to directory |

### chown & chgrp — Change Ownership

| Command | Description | Example |
|---|---|---|
| `chown user file` | Change file owner | `chown alice file.txt` |
| `chown user:group file` | Change owner and group | `chown alice:devs file.txt` |
| `chown -R user:group dir` | Recursively change ownership | `chown -R www-data:www-data /var/www` |
| `chgrp group file` | Change group only | `chgrp admins file.txt` |
| `chgrp -R group dir` | Recursively change group | `chgrp -R devs /srv/app` |

### Special Permissions

| Permission | Octal | Description |
|---|---|---|
| **SUID** (Set User ID) | `4xxx` e.g. `4755` | File runs as file owner (not executor). Used on `/usr/bin/passwd` |
| **SGID** (Set Group ID) | `2xxx` e.g. `2755` | File runs as group owner. On dir: new files inherit group |
| **Sticky Bit** | `1xxx` e.g. `1777` | Only file owner can delete in shared directories (e.g. `/tmp`) |

```bash
# Set SUID
chmod u+s file         # Symbolic
chmod 4755 file        # Numeric

# Set SGID
chmod g+s dir
chmod 2755 dir

# Set Sticky Bit
chmod +t /shared_dir
chmod 1777 /shared_dir

# Find SUID files (security audit)
find / -perm -4000 -type f 2>/dev/null

# Find SGID files
find / -perm -2000 -type f 2>/dev/null
```

### umask — Default Permission Mask

```bash
umask            # View current umask (e.g., 0022)
umask 027        # Set umask: files get 640, dirs get 750
umask 077        # Strict: files get 600, dirs get 700

# How umask works:
# Default file max: 666 → 666 - 022 = 644
# Default dir max:  777 → 777 - 022 = 755
```

---

## 6. User & Group Management

### User Account Commands

| Command | Description | Example |
|---|---|---|
| `whoami` | Print current username | `whoami` |
| `id` | Print UID, GID, groups | `id` |
| `id username` | Show another user's info | `id alice` |
| `w` | Who is logged in + activity | `w` |
| `who` | List logged in users | `who` |
| `last` | Login history | `last` |
| `last -n 10` | Last 10 logins | `last -n 10` |
| `lastlog` | Last login for all users | `lastlog` |
| `useradd` | Create new user | `sudo useradd alice` |
| `useradd -m` | Create user with home dir | `sudo useradd -m alice` |
| `useradd -m -s /bin/bash` | Create user with bash shell | `sudo useradd -m -s /bin/bash alice` |
| `useradd -G group` | Add user to supplementary group | `sudo useradd -G sudo alice` |
| `usermod -aG` | Add user to additional group | `sudo usermod -aG docker alice` |
| `usermod -l` | Rename user | `sudo usermod -l newname oldname` |
| `usermod -L` | Lock user account | `sudo usermod -L alice` |
| `usermod -U` | Unlock user account | `sudo usermod -U alice` |
| `usermod -s /bin/bash` | Change user shell | `sudo usermod -s /bin/bash alice` |
| `userdel` | Delete user | `sudo userdel alice` |
| `userdel -r` | Delete user and home directory | `sudo userdel -r alice` |
| `passwd` | Change own password | `passwd` |
| `passwd username` | Change another user's password | `sudo passwd alice` |
| `passwd -l` | Lock password | `sudo passwd -l alice` |
| `passwd -u` | Unlock password | `sudo passwd -u alice` |
| `passwd -e` | Expire password (force change on login) | `sudo passwd -e alice` |
| `su` | Switch user | `su alice` |
| `su -` | Switch to root with environment | `su -` |
| `sudo` | Run as superuser | `sudo apt update` |
| `sudo -i` | Open root shell | `sudo -i` |
| `sudo -l` | List sudo privileges | `sudo -l` |
| `visudo` | Edit sudoers file safely | `sudo visudo` |

### Group Commands

| Command | Description | Example |
|---|---|---|
| `groupadd` | Create new group | `sudo groupadd devs` |
| `groupmod -n` | Rename group | `sudo groupmod -n newname oldname` |
| `groupdel` | Delete group | `sudo groupdel devs` |
| `groups` | List groups current user belongs to | `groups` |
| `groups username` | List groups for specific user | `groups alice` |
| `gpasswd -a user group` | Add user to group | `sudo gpasswd -a alice devs` |
| `gpasswd -d user group` | Remove user from group | `sudo gpasswd -d alice devs` |

### Important User Files

| File | Description |
|---|---|
| `/etc/passwd` | User account info (username, UID, GID, home, shell) |
| `/etc/shadow` | Encrypted passwords + aging policy |
| `/etc/group` | Group definitions |
| `/etc/gshadow` | Secure group info |
| `/etc/sudoers` | Sudo access rules |
| `/etc/login.defs` | Default user creation settings |
| `/etc/skel/` | Template files copied to new user home dirs |

---

## 7. Process Management

### Viewing Processes

| Command | Description | Example |
|---|---|---|
| `ps` | Show processes for current user | `ps` |
| `ps aux` | Show all processes (BSD style) | `ps aux` |
| `ps -ef` | Show all processes (full format) | `ps -ef` |
| `ps -u username` | Processes by user | `ps -u alice` |
| `ps --forest` | Show process tree | `ps --forest` |
| `top` | Interactive real-time process viewer | `top` |
| `htop` | Enhanced interactive viewer (if installed) | `htop` |
| `pgrep` | Find process by name | `pgrep nginx` |
| `pgrep -l` | Find PID with process name | `pgrep -l ssh` |
| `pidof` | Get PID of a running program | `pidof nginx` |
| `pstree` | Display process tree | `pstree` |
| `pstree -p` | Process tree with PIDs | `pstree -p` |

### Controlling Processes

| Command | Description | Example |
|---|---|---|
| `kill PID` | Send SIGTERM (graceful) to process | `kill 1234` |
| `kill -9 PID` | Send SIGKILL (force kill) | `kill -9 1234` |
| `kill -l` | List all signals | `kill -l` |
| `killall name` | Kill all processes by name | `killall nginx` |
| `pkill name` | Kill by name/pattern | `pkill -f "python script.py"` |
| `pkill -u user` | Kill all processes of a user | `pkill -u alice` |
| `nice` | Start process with priority | `nice -n 10 command` |
| `renice` | Change priority of running process | `renice -n 5 -p 1234` |

### Background & Job Control

| Command | Description | Example |
|---|---|---|
| `command &` | Run in background | `./script.sh &` |
| `jobs` | List background jobs | `jobs` |
| `fg` | Bring job to foreground | `fg %1` |
| `bg` | Resume stopped job in background | `bg %1` |
| `Ctrl+Z` | Suspend running process | *(keyboard shortcut)* |
| `Ctrl+C` | Interrupt/terminate process | *(keyboard shortcut)* |
| `nohup` | Run ignoring hangup signal | `nohup ./script.sh &` |
| `disown` | Detach job from shell | `disown %1` |
| `wait` | Wait for background jobs to finish | `wait` |
| `screen` | Terminal multiplexer (persistent sessions) | `screen -S mysession` |
| `tmux` | Advanced terminal multiplexer | `tmux new -s work` |

### Signal Reference

| Signal | Number | Description |
|---|---|---|
| SIGHUP | 1 | Hangup / reload config |
| SIGINT | 2 | Interrupt (Ctrl+C) |
| SIGQUIT | 3 | Quit with core dump |
| SIGKILL | 9 | Immediate kill (unblockable) |
| SIGTERM | 15 | Graceful termination (default) |
| SIGSTOP | 19 | Stop process (unblockable) |
| SIGCONT | 18 | Continue stopped process |
| SIGUSR1 | 10 | User-defined signal 1 |
| SIGUSR2 | 12 | User-defined signal 2 |

---

## 8. System Information & Monitoring

### System Info

| Command | Description | Example |
|---|---|---|
| `uname -a` | All kernel info | `uname -a` |
| `uname -r` | Kernel version only | `uname -r` |
| `uname -m` | Machine hardware name | `uname -m` |
| `hostname` | Show/set hostname | `hostname` |
| `hostname -I` | Show all IP addresses | `hostname -I` |
| `hostnamectl` | Detailed hostname info (systemd) | `hostnamectl` |
| `uptime` | System uptime + load average | `uptime` |
| `date` | Current date and time | `date` |
| `date +"%Y-%m-%d %H:%M:%S"` | Formatted date | `date +"%Y-%m-%d %H:%M:%S"` |
| `timedatectl` | Timezone and time info | `timedatectl` |
| `cal` | Calendar | `cal` |
| `lscpu` | CPU info | `lscpu` |
| `lsmem` | Memory layout | `lsmem` |
| `lshw` | Detailed hardware info | `sudo lshw` |
| `lsblk` | Block device (disk) info | `lsblk` |
| `lsusb` | USB device info | `lsusb` |
| `lspci` | PCI device info | `lspci` |
| `dmidecode` | Hardware BIOS/SMBIOS info | `sudo dmidecode -t memory` |
| `arch` | Print system architecture | `arch` |
| `nproc` | Number of CPU cores | `nproc` |

### Memory & CPU Monitoring

| Command | Description | Example |
|---|---|---|
| `free` | Memory usage | `free` |
| `free -h` | Human-readable memory usage | `free -h` |
| `free -m` | Memory in MB | `free -m` |
| `vmstat` | Virtual memory statistics | `vmstat 2 5` |
| `vmstat -s` | Memory usage summary | `vmstat -s` |
| `top` | Live system resource usage | `top` |
| `top -u alice` | Top for specific user | `top -u alice` |
| `/proc/cpuinfo` | Raw CPU info | `cat /proc/cpuinfo` |
| `/proc/meminfo` | Raw memory info | `cat /proc/meminfo` |
| `/proc/loadavg` | Load average | `cat /proc/loadavg` |
| `iostat` | CPU and I/O statistics | `iostat -x 2` |
| `sar` | System Activity Report | `sar -u 1 5` |
| `mpstat` | Per-CPU statistics | `mpstat -P ALL` |

---

## 9. Disk & Storage Management

### Disk Usage

| Command | Description | Example |
|---|---|---|
| `df` | Disk space usage (filesystems) | `df` |
| `df -h` | Human-readable disk usage | `df -h` |
| `df -T` | Show filesystem type | `df -T` |
| `du` | Directory/file disk usage | `du /var` |
| `du -h` | Human-readable | `du -h /var` |
| `du -sh` | Summary of directory total | `du -sh /var/log` |
| `du -ah` | All files, human-readable | `du -ah /home/alice` |
| `du --max-depth=1 -h` | One level deep only | `du --max-depth=1 -h /var` |
| `ncdu` | Interactive ncurses disk usage | `ncdu /` |

### Partitions & Filesystems

| Command | Description | Example |
|---|---|---|
| `lsblk` | List block devices (disks/partitions) | `lsblk` |
| `lsblk -f` | Show filesystem info | `lsblk -f` |
| `blkid` | Show block device UUID/type | `sudo blkid` |
| `fdisk -l` | List partition table | `sudo fdisk -l` |
| `fdisk /dev/sdb` | Partition a disk interactively | `sudo fdisk /dev/sdb` |
| `parted` | Advanced partitioning tool | `sudo parted /dev/sda print` |
| `mkfs.ext4` | Format partition as ext4 | `sudo mkfs.ext4 /dev/sdb1` |
| `mkfs.xfs` | Format as XFS | `sudo mkfs.xfs /dev/sdb1` |
| `mkfs.vfat` | Format as FAT32 | `sudo mkfs.vfat /dev/sdb1` |
| `fsck` | Filesystem check/repair | `sudo fsck /dev/sdb1` |
| `e2fsck` | Check ext2/3/4 filesystem | `sudo e2fsck -f /dev/sdb1` |
| `tune2fs` | Adjust ext filesystem parameters | `sudo tune2fs -l /dev/sda1` |

### Mounting

| Command | Description | Example |
|---|---|---|
| `mount` | Mount filesystem | `sudo mount /dev/sdb1 /mnt/usb` |
| `mount -t` | Specify filesystem type | `sudo mount -t ext4 /dev/sdb1 /mnt` |
| `mount -o ro` | Mount read-only | `sudo mount -o ro /dev/sdb1 /mnt` |
| `umount` | Unmount filesystem | `sudo umount /mnt/usb` |
| `umount -l` | Lazy unmount | `sudo umount -l /mnt` |
| `findmnt` | Show mount points (tree) | `findmnt` |
| `/etc/fstab` | Auto-mount config at boot | `cat /etc/fstab` |
| `mount -a` | Mount all entries in /etc/fstab | `sudo mount -a` |

### Swap Management

| Command | Description | Example |
|---|---|---|
| `swapon --show` | List swap usage | `swapon --show` |
| `free -h` | View swap + RAM | `free -h` |
| `mkswap /dev/sdb2` | Create swap partition | `sudo mkswap /dev/sdb2` |
| `swapon /dev/sdb2` | Enable swap | `sudo swapon /dev/sdb2` |
| `swapoff /dev/sdb2` | Disable swap | `sudo swapoff /dev/sdb2` |
| `fallocate -l 2G /swapfile` | Create swap file | `sudo fallocate -l 2G /swapfile` |

### LVM (Logical Volume Manager)

| Command | Description |
|---|---|
| `pvs` | List physical volumes |
| `vgs` | List volume groups |
| `lvs` | List logical volumes |
| `pvcreate /dev/sdb` | Initialize physical volume |
| `vgcreate myvg /dev/sdb` | Create volume group |
| `lvcreate -L 10G -n mylv myvg` | Create logical volume |
| `lvextend -L +5G /dev/myvg/mylv` | Extend logical volume |
| `resize2fs /dev/myvg/mylv` | Resize ext4 filesystem |
| `lvremove /dev/myvg/mylv` | Remove logical volume |

---

## 10. Networking Commands

### Network Interface & Configuration

| Command | Description | Example |
|---|---|---|
| `ip a` | Show all network interfaces & IPs | `ip a` |
| `ip addr show` | Same as `ip a` | `ip addr show eth0` |
| `ip link` | Show link-layer info | `ip link show` |
| `ip link set eth0 up` | Bring interface up | `sudo ip link set eth0 up` |
| `ip link set eth0 down` | Bring interface down | `sudo ip link set eth0 down` |
| `ip route` | Show routing table | `ip route` |
| `ip route add` | Add static route | `sudo ip route add 10.0.0.0/8 via 192.168.1.1` |
| `ip route del` | Delete route | `sudo ip route del 10.0.0.0/8` |
| `ifconfig` | Show/configure interfaces (legacy) | `ifconfig eth0` |
| `ifconfig eth0 up/down` | Enable/disable interface | `sudo ifconfig eth0 down` |
| `nmcli` | NetworkManager CLI | `nmcli con show` |
| `nmtui` | NetworkManager text UI | `nmtui` |
| `ethtool eth0` | NIC stats and settings | `sudo ethtool eth0` |

### DNS & Hostname Resolution

| Command | Description | Example |
|---|---|---|
| `nslookup` | DNS query tool | `nslookup google.com` |
| `dig` | Detailed DNS lookup | `dig google.com` |
| `dig +short` | Short DNS answer | `dig +short google.com` |
| `dig MX` | Query MX record | `dig google.com MX` |
| `dig @8.8.8.8` | Query specific DNS server | `dig @8.8.8.8 google.com` |
| `host` | Simple DNS lookup | `host google.com` |
| `resolvectl` | DNS resolver info (systemd) | `resolvectl status` |
| `/etc/hosts` | Local host name resolution | `cat /etc/hosts` |
| `/etc/resolv.conf` | DNS server config | `cat /etc/resolv.conf` |
| `/etc/nsswitch.conf` | Name service switch config | `cat /etc/nsswitch.conf` |

### Connectivity & Diagnostics

| Command | Description | Example |
|---|---|---|
| `ping` | Test host reachability (ICMP) | `ping google.com` |
| `ping -c 4` | Ping 4 times only | `ping -c 4 8.8.8.8` |
| `ping -i 0.5` | Ping every 0.5 seconds | `ping -i 0.5 host` |
| `traceroute` | Trace network path to host | `traceroute google.com` |
| `tracepath` | Trace path (no root needed) | `tracepath google.com` |
| `mtr` | Combines ping + traceroute (live) | `mtr google.com` |
| `curl` | Transfer data via URL | `curl https://example.com` |
| `curl -I` | Fetch only HTTP headers | `curl -I https://example.com` |
| `curl -o` | Save output to file | `curl -o output.html https://example.com` |
| `curl -L` | Follow redirects | `curl -L https://example.com` |
| `wget` | Download file from URL | `wget https://example.com/file.zip` |
| `wget -r` | Recursive download | `wget -r https://example.com` |
| `wget -c` | Resume interrupted download | `wget -c https://example.com/file.zip` |

### Ports & Sockets

| Command | Description | Example |
|---|---|---|
| `ss` | Socket statistics (modern netstat) | `ss -tulnp` |
| `ss -t` | TCP sockets only | `ss -tn` |
| `ss -u` | UDP sockets only | `ss -un` |
| `ss -l` | Listening sockets | `ss -lnp` |
| `ss -p` | Show process using socket | `ss -tlnp` |
| `netstat` | Legacy network stats (deprecated) | `netstat -tulnp` |
| `netstat -rn` | Show routing table | `netstat -rn` |
| `lsof -i` | List open network files/ports | `sudo lsof -i :80` |
| `lsof -i :22` | Who's using port 22 | `sudo lsof -i :22` |
| `nmap` | Network port scanner | `nmap -sV 192.168.1.1` |
| `nmap -sn` | Ping scan (host discovery) | `nmap -sn 192.168.1.0/24` |
| `nmap -p 1-1000` | Scan port range | `nmap -p 1-1000 192.168.1.1` |
| `nc` (netcat) | Network Swiss army knife | `nc -zv host 80` |
| `nc -l -p 4444` | Listen on port | `nc -l -p 4444` |

### Firewall

| Command | Description | Example |
|---|---|---|
| `ufw status` | Check firewall status | `sudo ufw status` |
| `ufw enable` | Enable firewall | `sudo ufw enable` |
| `ufw disable` | Disable firewall | `sudo ufw disable` |
| `ufw allow 22` | Allow SSH port | `sudo ufw allow 22` |
| `ufw allow 80/tcp` | Allow HTTP | `sudo ufw allow 80/tcp` |
| `ufw deny 23` | Deny telnet | `sudo ufw deny 23` |
| `ufw delete allow 80` | Remove rule | `sudo ufw delete allow 80` |
| `ufw reset` | Reset all rules | `sudo ufw reset` |
| `iptables -L` | List all rules | `sudo iptables -L -n -v` |
| `iptables -A INPUT` | Append input rule | `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT` |
| `iptables -D INPUT` | Delete input rule | `sudo iptables -D INPUT 1` |
| `iptables-save` | Save rules to file | `sudo iptables-save > /etc/iptables/rules.v4` |
| `firewall-cmd` | firewalld (RHEL/CentOS) | `sudo firewall-cmd --state` |

---

## 11. Package Management

### Debian / Ubuntu (APT)

| Command | Description |
|---|---|
| `apt update` | Refresh package index |
| `apt upgrade` | Upgrade installed packages |
| `apt full-upgrade` | Upgrade with dependency changes |
| `apt install pkg` | Install package |
| `apt install -y pkg` | Install without confirmation prompt |
| `apt remove pkg` | Remove package (keep config) |
| `apt purge pkg` | Remove package + config files |
| `apt autoremove` | Remove orphaned/unused packages |
| `apt search pkg` | Search for a package |
| `apt show pkg` | Show package details |
| `apt list --installed` | List installed packages |
| `apt list --upgradable` | List upgradable packages |
| `apt-cache depends pkg` | Show package dependencies |
| `apt-cache rdepends pkg` | Show reverse dependencies |
| `dpkg -i pkg.deb` | Install local .deb file |
| `dpkg -r pkg` | Remove installed .deb package |
| `dpkg -l` | List all installed packages |
| `dpkg -L pkg` | List files installed by package |
| `dpkg -S /path/to/file` | Find which package owns a file |
| `apt-get dist-upgrade` | Distribution-level upgrade |

### RHEL / CentOS / Fedora (YUM / DNF)

| Command | Description |
|---|---|
| `dnf update` | Update all packages |
| `dnf install pkg` | Install package |
| `dnf remove pkg` | Remove package |
| `dnf search pkg` | Search packages |
| `dnf info pkg` | Show package info |
| `dnf list installed` | List installed packages |
| `dnf provides /bin/ls` | Find which package owns a file |
| `dnf autoremove` | Remove unused packages |
| `dnf group list` | List package groups |
| `dnf group install "Development Tools"` | Install a package group |
| `rpm -ivh pkg.rpm` | Install local RPM |
| `rpm -qa` | List all installed RPMs |
| `rpm -qi pkg` | RPM package info |
| `rpm -ql pkg` | Files installed by RPM |
| `rpm -qf /path/to/file` | Find RPM owning a file |
| `yum update` | Legacy: update packages (RHEL 7) |
| `yum install pkg` | Legacy: install package |

### Snap & Flatpak (Universal Packages)

| Command | Description |
|---|---|
| `snap install pkg` | Install snap package |
| `snap remove pkg` | Remove snap |
| `snap list` | List installed snaps |
| `snap refresh` | Update all snaps |
| `flatpak install pkg` | Install flatpak |
| `flatpak list` | List installed flatpaks |
| `flatpak update` | Update all flatpaks |

---

## 12. Archiving & Compression

### tar — Tape Archive

| Command | Description | Example |
|---|---|---|
| `tar -cvf` | Create archive | `tar -cvf archive.tar /dir/` |
| `tar -xvf` | Extract archive | `tar -xvf archive.tar` |
| `tar -tvf` | List archive contents | `tar -tvf archive.tar` |
| `tar -czvf` | Create gzip compressed archive | `tar -czvf archive.tar.gz /dir/` |
| `tar -xzvf` | Extract gzip archive | `tar -xzvf archive.tar.gz` |
| `tar -cjvf` | Create bzip2 compressed archive | `tar -cjvf archive.tar.bz2 /dir/` |
| `tar -xjvf` | Extract bzip2 archive | `tar -xjvf archive.tar.bz2` |
| `tar -cJvf` | Create xz compressed archive | `tar -cJvf archive.tar.xz /dir/` |
| `tar -xJvf` | Extract xz archive | `tar -xJvf archive.tar.xz` |
| `tar -C /dest` | Extract to specific directory | `tar -xzvf arch.tar.gz -C /opt/` |
| `tar --exclude` | Exclude files/dirs | `tar -czvf archive.tar.gz dir/ --exclude="*.log"` |

> **Flag Reference**: `-c` create, `-x` extract, `-v` verbose, `-f` file, `-z` gzip, `-j` bzip2, `-J` xz, `-t` list

### gzip / gunzip

| Command | Description |
|---|---|
| `gzip file` | Compress file (replaces original) |
| `gzip -k file` | Keep original file |
| `gzip -d file.gz` | Decompress (same as gunzip) |
| `gunzip file.gz` | Decompress |
| `gzip -l file.gz` | Show compression info |
| `zcat file.gz` | View compressed file without extracting |

### bzip2 / bunzip2

| Command | Description |
|---|---|
| `bzip2 file` | Compress file |
| `bzip2 -k file` | Keep original |
| `bzip2 -d file.bz2` | Decompress |
| `bunzip2 file.bz2` | Decompress |
| `bzcat file.bz2` | View without extracting |

### zip / unzip

| Command | Description |
|---|---|
| `zip archive.zip file1 file2` | Create zip archive |
| `zip -r archive.zip dir/` | Recursively zip directory |
| `zip -e archive.zip file` | Create encrypted zip |
| `unzip archive.zip` | Extract zip archive |
| `unzip -l archive.zip` | List zip contents |
| `unzip -d /dest archive.zip` | Extract to directory |

---

## 13. Text Processing & Manipulation

### sed — Stream Editor

```bash
# Basic substitution
sed 's/old/new/' file.txt           # Replace first occurrence per line
sed 's/old/new/g' file.txt          # Replace all occurrences (global)
sed 's/old/new/gi' file.txt         # Case-insensitive global replace
sed -i 's/old/new/g' file.txt       # In-place edit (modify file directly)
sed -i.bak 's/old/new/g' file.txt   # In-place with backup

# Delete lines
sed '/pattern/d' file.txt           # Delete lines matching pattern
sed '5d' file.txt                   # Delete line 5
sed '1,5d' file.txt                 # Delete lines 1-5

# Print specific lines
sed -n '5p' file.txt                # Print only line 5
sed -n '1,10p' file.txt             # Print lines 1-10
sed -n '/error/p' file.txt          # Print matching lines

# Insert / append
sed '3i\inserted line' file.txt     # Insert before line 3
sed '3a\appended line' file.txt     # Append after line 3

# Multiple operations
sed -e 's/foo/bar/' -e 's/baz/qux/' file.txt
```

### awk — Pattern Processing

```bash
# Print columns
awk '{print $1}' file.txt           # Print first column
awk '{print $1, $3}' file.txt       # Print columns 1 and 3
awk '{print NR, $0}' file.txt       # Print with line numbers

# Field separator
awk -F: '{print $1, $3}' /etc/passwd   # Use : as delimiter
awk -F',' '{print $2}' data.csv         # CSV processing

# Filtering
awk '$3 > 1000' /etc/passwd             # Lines where column 3 > 1000
awk '/error/ {print $0}' file.log       # Print lines matching pattern
awk 'NR==5' file.txt                    # Print line 5 only

# Arithmetic
awk '{sum += $1} END {print sum}' numbers.txt   # Sum column
awk '{print $1 * $2}' data.txt                  # Multiply columns
awk 'END {print NR}' file.txt                   # Count lines

# Built-in variables
# NR = current line number
# NF = number of fields in current line
# FS = field separator
# RS = record separator
# OFS = output field separator
awk 'BEGIN{OFS=","} {print $1,$2,$3}' file.txt  # Output CSV
```

### cut, paste, join, sort, uniq

```bash
# cut — extract columns/fields
cut -d: -f1 /etc/passwd             # Extract field 1 (delimiter :)
cut -d, -f1,3 file.csv              # Extract fields 1 and 3
cut -c1-10 file.txt                  # Extract characters 1-10

# sort — sort lines
sort file.txt                        # Alphabetical sort
sort -r file.txt                     # Reverse sort
sort -n file.txt                     # Numeric sort
sort -k2 file.txt                    # Sort by column 2
sort -t: -k3 -n /etc/passwd          # Sort by UID (field 3)
sort -u file.txt                     # Sort + remove duplicates

# uniq — filter duplicate lines (requires sorted input)
uniq file.txt                        # Remove consecutive duplicates
uniq -c file.txt                     # Count occurrences
uniq -d file.txt                     # Show only duplicates
uniq -u file.txt                     # Show only unique lines

# Combined: count frequency
sort file.txt | uniq -c | sort -rn   # Most frequent to least

# paste — merge lines side by side
paste file1.txt file2.txt            # Merge files column-wise
paste -d, file1.txt file2.txt        # Use comma delimiter

# join — join files on common field
join file1.txt file2.txt             # Join on first field
```

### tr, tee, xargs

```bash
# tr — translate or delete characters
echo "Hello World" | tr 'a-z' 'A-Z'   # Convert to uppercase
echo "Hello World" | tr 'A-Z' 'a-z'   # Convert to lowercase
echo "hello   world" | tr -s ' '       # Squeeze multiple spaces
echo "hello123" | tr -d '0-9'          # Delete digits
cat file.txt | tr -cd 'a-zA-Z\n'       # Keep only letters

# tee — write to stdout and file simultaneously
command | tee output.txt               # Write to file and display
command | tee -a output.txt            # Append to file

# xargs — build and execute command from stdin
find . -name "*.log" | xargs rm        # Delete all log files
find . -type f | xargs grep "error"    # Search in found files
echo "file1 file2" | xargs -n1 wc -l  # Pass one arg at a time
cat urls.txt | xargs -P 4 -I{} curl {} # Parallel curl 4 at a time
```

### diff & patch

```bash
diff file1.txt file2.txt              # Show differences
diff -u file1.txt file2.txt           # Unified diff format (better)
diff -r dir1/ dir2/                   # Recursive directory diff
diff -i file1 file2                   # Case-insensitive diff
diff --color file1 file2              # Colored output

# Create a patch
diff -u original.txt modified.txt > changes.patch

# Apply a patch
patch < changes.patch
patch -p1 < changes.patch             # Strip 1 leading path component

# Compare sorted content
comm file1.txt file2.txt              # Lines unique to each + common
comm -12 file1.txt file2.txt          # Only common lines
```

---

## 14. Shell & Environment

### Variables & Environment

```bash
# Variables
NAME="Alice"                     # Set variable
echo $NAME                       # Use variable
echo "${NAME}_suffix"            # Variable in string
unset NAME                       # Remove variable

# Environment variables
export VAR="value"               # Export to child processes
env                              # List all environment variables
printenv PATH                    # Print specific env var
printenv                         # List all env vars

# Common environment variables
echo $HOME        # User home directory
echo $USER        # Current username
echo $SHELL       # Current shell
echo $PATH        # Executable search path
echo $PWD         # Current working directory
echo $OLDPWD      # Previous directory
echo $HOSTNAME    # System hostname
echo $TERM        # Terminal type
echo $EDITOR      # Default text editor
echo $LANG        # System language/locale
echo $$           # PID of current shell
echo $?           # Exit code of last command
echo $!           # PID of last background process

# Add to PATH
export PATH=$PATH:/new/directory

# Make permanent (add to ~/.bashrc or ~/.bash_profile)
echo 'export PATH=$PATH:/opt/myapp/bin' >> ~/.bashrc
source ~/.bashrc
```

### Shell Configuration Files

| File | Purpose |
|---|---|
| `~/.bashrc` | Interactive non-login bash shell config |
| `~/.bash_profile` | Login shell config (runs once at login) |
| `~/.bash_aliases` | Alias definitions (sourced from .bashrc) |
| `~/.bash_history` | Command history file |
| `~/.profile` | Generic shell profile (login shells) |
| `/etc/bash.bashrc` | System-wide bash config |
| `/etc/profile` | System-wide login shell config |
| `/etc/environment` | System-wide environment variables |
| `/etc/profile.d/*.sh` | Additional profile scripts |

### Aliases & Functions

```bash
# Temporary alias (current session)
alias ll='ls -la'
alias grep='grep --color=auto'
alias ..='cd ..'
alias ...='cd ../..'
alias update='sudo apt update && sudo apt upgrade -y'

# Unalias
unalias ll

# List all aliases
alias

# Shell function
greet() {
    echo "Hello, $1!"
}
greet "World"     # Output: Hello, World!

# Add permanently to ~/.bashrc
echo "alias ll='ls -la'" >> ~/.bashrc
source ~/.bashrc
```

### History

| Command | Description |
|---|---|
| `history` | Show command history |
| `history 20` | Show last 20 commands |
| `history -c` | Clear history |
| `!!` | Repeat last command |
| `!n` | Run command number n |
| `!string` | Run last command starting with string |
| `Ctrl+R` | Reverse search history |
| `HISTSIZE=5000` | Set history size |

---

## 15. System Services (systemd / init)

### systemctl — Service Management

| Command | Description | Example |
|---|---|---|
| `systemctl start svc` | Start a service | `sudo systemctl start nginx` |
| `systemctl stop svc` | Stop a service | `sudo systemctl stop nginx` |
| `systemctl restart svc` | Restart a service | `sudo systemctl restart nginx` |
| `systemctl reload svc` | Reload config (no restart) | `sudo systemctl reload nginx` |
| `systemctl enable svc` | Enable at boot | `sudo systemctl enable nginx` |
| `systemctl disable svc` | Disable at boot | `sudo systemctl disable nginx` |
| `systemctl status svc` | Show service status | `systemctl status nginx` |
| `systemctl is-active svc` | Check if running | `systemctl is-active nginx` |
| `systemctl is-enabled svc` | Check if enabled | `systemctl is-enabled nginx` |
| `systemctl list-units` | List all units | `systemctl list-units --type=service` |
| `systemctl list-unit-files` | List all unit files | `systemctl list-unit-files` |
| `systemctl daemon-reload` | Reload systemd config | `sudo systemctl daemon-reload` |
| `systemctl mask svc` | Prevent service from starting | `sudo systemctl mask telnet` |
| `systemctl unmask svc` | Unmask service | `sudo systemctl unmask telnet` |

### journalctl — Log Viewer (systemd)

| Command | Description |
|---|---|
| `journalctl` | View all logs |
| `journalctl -f` | Follow (real-time) logs |
| `journalctl -u nginx` | Logs for specific service |
| `journalctl -u nginx -f` | Follow service logs |
| `journalctl --since "1 hour ago"` | Logs from last hour |
| `journalctl --since "2024-01-01"` | Logs since date |
| `journalctl -p err` | Only error-level logs |
| `journalctl -p warning` | Warning and above |
| `journalctl -n 50` | Last 50 log entries |
| `journalctl -b` | Logs since last boot |
| `journalctl -b -1` | Logs from previous boot |
| `journalctl --disk-usage` | Show journal disk usage |
| `journalctl --vacuum-size=500M` | Limit journal size |

### System Control

| Command | Description |
|---|---|
| `systemctl poweroff` | Shutdown system |
| `systemctl reboot` | Reboot system |
| `systemctl suspend` | Suspend to RAM |
| `systemctl hibernate` | Hibernate to disk |
| `shutdown -h now` | Shutdown immediately |
| `shutdown -r now` | Reboot immediately |
| `shutdown -h +10` | Shutdown in 10 minutes |
| `shutdown -c` | Cancel scheduled shutdown |
| `reboot` | Reboot system |
| `halt` | Halt the system |
| `init 0` | Halt (SysV) |
| `init 6` | Reboot (SysV) |
| `runlevel` | Show current runlevel |

---

## 16. SSH & Remote Access

### SSH Basics

| Command | Description | Example |
|---|---|---|
| `ssh user@host` | Connect to remote host | `ssh alice@192.168.1.10` |
| `ssh -p 2222 user@host` | Connect on custom port | `ssh -p 2222 alice@host` |
| `ssh -i key.pem user@host` | Use private key | `ssh -i ~/.ssh/mykey.pem alice@host` |
| `ssh -X user@host` | Enable X11 forwarding (GUI) | `ssh -X alice@host` |
| `ssh -L` | Local port forwarding | `ssh -L 8080:localhost:80 alice@host` |
| `ssh -R` | Remote port forwarding | `ssh -R 9090:localhost:3000 alice@host` |
| `ssh -D` | Dynamic (SOCKS proxy) | `ssh -D 1080 alice@host` |
| `ssh -N` | No command (just tunnel) | `ssh -N -L 8080:localhost:80 alice@host` |
| `ssh -v` | Verbose (debug) | `ssh -v alice@host` |
| `ssh -J jump@host` | SSH jump host (ProxyJump) | `ssh -J jump.example.com alice@target` |

### SSH Key Management

```bash
# Generate key pair
ssh-keygen -t rsa -b 4096 -C "your@email.com"      # RSA 4096-bit
ssh-keygen -t ed25519 -C "your@email.com"            # ED25519 (modern)
ssh-keygen -t ecdsa -b 521                            # ECDSA

# Copy public key to remote server
ssh-copy-id user@host                                 # Standard method
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host        # Specific key
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Key permissions (critical!)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys

# SSH Agent
eval $(ssh-agent)              # Start SSH agent
ssh-add ~/.ssh/id_rsa          # Add key to agent
ssh-add -l                     # List keys in agent
ssh-add -d ~/.ssh/id_rsa       # Remove key from agent
ssh-add -D                     # Remove all keys
```

### SSH Config File (~/.ssh/config)

```
Host myserver
    HostName 192.168.1.100
    User alice
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    ForwardAgent yes

Host jump-via-bastion
    HostName internal.server.com
    User developer
    ProxyJump bastion.example.com
```

### SCP & SFTP — Secure File Transfer

```bash
# SCP (Secure Copy)
scp file.txt user@host:/remote/path/          # Upload file
scp user@host:/remote/file.txt ./             # Download file
scp -r local_dir/ user@host:/remote/path/     # Upload directory
scp -P 2222 file.txt user@host:/path/         # Custom port
scp -i key.pem file.txt user@host:/path/      # With private key

# SFTP
sftp user@host                                # Open SFTP session
sftp> ls                                      # List remote files
sftp> get remote_file.txt                     # Download
sftp> put local_file.txt                      # Upload
sftp> mkdir new_dir                           # Create remote dir
sftp> exit                                    # Exit SFTP

# rsync — efficient file sync
rsync -avz local/ user@host:/remote/           # Sync to remote
rsync -avz user@host:/remote/ local/           # Sync from remote
rsync -avz --delete local/ user@host:/remote/  # Mirror (delete removed)
rsync -avz --progress file user@host:/path/    # Show progress
rsync -avz -e "ssh -p 2222" local/ user@host:/remote/  # Custom port
```

### SSH Server Configuration

```bash
# Key config file
sudo nano /etc/ssh/sshd_config

# Important settings:
Port 22                         # Change default port
PermitRootLogin no              # Disable root login
PasswordAuthentication no       # Key-only auth
PubkeyAuthentication yes        # Enable key auth
AllowUsers alice bob            # Whitelist users
MaxAuthTries 3                  # Limit login attempts
ClientAliveInterval 300         # Keepalive timeout
X11Forwarding no                # Disable X11 if not needed

# Apply changes
sudo systemctl restart sshd

# Test config before restarting
sudo sshd -t
```

---

## 17. Cron Jobs & Scheduling

### Cron Syntax

```
* * * * * command_to_execute
│ │ │ │ │
│ │ │ │ └── Day of week (0-7, 0 & 7 = Sunday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

Special strings:
@reboot     → Run at startup
@hourly     → Run every hour  (0 * * * *)
@daily      → Run daily       (0 0 * * *)
@weekly     → Run weekly      (0 0 * * 0)
@monthly    → Run monthly     (0 0 1 * *)
@yearly     → Run yearly      (0 0 1 1 *)
```

### Cron Commands

| Command | Description |
|---|---|
| `crontab -e` | Edit user's crontab |
| `crontab -l` | List user's crontab |
| `crontab -r` | Remove user's crontab |
| `crontab -u alice -e` | Edit another user's crontab (root) |
| `crontab -u alice -l` | List another user's crontab |

### Cron Examples

```bash
# Run script every minute
* * * * * /home/alice/script.sh

# Run at 2:30 AM every day
30 2 * * * /usr/bin/backup.sh

# Run every hour
0 * * * * /usr/bin/cleanup.sh

# Run every 15 minutes
*/15 * * * * /usr/bin/check.sh

# Run at 8 AM on weekdays (Mon-Fri)
0 8 * * 1-5 /usr/bin/report.sh

# Run on 1st of every month
0 0 1 * * /usr/bin/monthly_backup.sh

# Run at reboot
@reboot /home/alice/startup.sh

# Log output
* * * * * /usr/bin/script.sh >> /var/log/cron_output.log 2>&1

# System cron directories
/etc/cron.d/           # System cron fragments
/etc/cron.daily/       # Scripts run daily
/etc/cron.hourly/      # Scripts run hourly
/etc/cron.weekly/      # Scripts run weekly
/etc/cron.monthly/     # Scripts run monthly
```

### at — One-time Scheduling

```bash
at 10:00 AM                   # Schedule at specific time
at now + 5 minutes            # Schedule relative time
at now + 1 hour               # One hour from now
at midnight                   # At midnight
at> command_to_run
at> Ctrl+D                    # Save and exit

atq                           # List scheduled jobs
atrm 3                        # Remove job number 3
```

---

## 18. Log Management

### Key Log Files

| Log File | Description |
|---|---|
| `/var/log/syslog` | General system messages (Debian/Ubuntu) |
| `/var/log/messages` | General system messages (RHEL/CentOS) |
| `/var/log/auth.log` | Authentication events (Debian/Ubuntu) |
| `/var/log/secure` | Authentication events (RHEL/CentOS) |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/dmesg` | Boot/kernel ring buffer |
| `/var/log/boot.log` | Boot process log |
| `/var/log/cron` | Cron job execution log |
| `/var/log/maillog` | Mail server log |
| `/var/log/httpd/` | Apache web server logs |
| `/var/log/nginx/` | Nginx web server logs |
| `/var/log/mysql/` | MySQL database logs |
| `/var/log/faillog` | Failed login attempts |
| `/var/log/lastlog` | Last login info |
| `/var/log/wtmp` | Login/logout history |
| `/var/log/btmp` | Bad login attempts |

### Log Analysis Commands

```bash
# Real-time monitoring
tail -f /var/log/syslog
journalctl -f
journalctl -f -u nginx

# View authentication failures
grep "Failed password" /var/log/auth.log
grep "authentication failure" /var/log/auth.log
lastb                                     # Failed logins from /var/log/btmp

# View successful logins
grep "Accepted password" /var/log/auth.log
last                                      # Successful logins

# Analyze log frequency
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head

# Filter by date
grep "May 22" /var/log/syslog
journalctl --since "2024-05-22" --until "2024-05-23"

# dmesg — Kernel ring buffer
dmesg                                     # All kernel messages
dmesg | tail -20                          # Recent kernel messages
dmesg -T                                  # With human-readable timestamps
dmesg --level err,warn                    # Filter by level
dmesg -w                                  # Follow live

# logrotate — log rotation management
logrotate /etc/logrotate.conf             # Run manually
logrotate -d /etc/logrotate.conf          # Dry run
cat /etc/logrotate.d/nginx                # View rotation config
```

---

## 19. Security & Hardening Commands

### File Integrity & Auditing

```bash
# md5sum / sha256sum — file integrity
md5sum file.txt                         # Generate MD5 hash
sha256sum file.txt                      # Generate SHA256 hash
sha512sum file.txt                      # Generate SHA512 hash
md5sum -c checksums.md5                 # Verify from checksum file

# Find world-writable files (security risk)
find / -perm -o+w -type f 2>/dev/null

# Find SUID/SGID files
find / -perm -4000 -type f 2>/dev/null  # SUID
find / -perm -2000 -type f 2>/dev/null  # SGID

# Find files with no owner
find / -nouser -o -nogroup 2>/dev/null

# Find recently modified files
find / -mtime -1 -type f 2>/dev/null    # Modified in last 24 hours

# auditd — Linux Audit Framework
sudo apt install auditd
sudo systemctl start auditd
auditctl -l                             # List audit rules
auditctl -w /etc/passwd -p wa -k passwd_change   # Watch file
ausearch -k passwd_change              # Search audit logs
aureport                               # Summary audit report
aureport --auth                        # Authentication report
```

### SELinux & AppArmor

```bash
# SELinux (RHEL/CentOS)
getenforce                              # Get SELinux mode
setenforce 1                            # Set Enforcing mode
setenforce 0                            # Set Permissive mode
sestatus                                # SELinux status
cat /etc/selinux/config                 # SELinux config
chcon -t httpd_sys_content_t /var/www/  # Change file context
restorecon -R /var/www/                 # Restore default context
semanage port -l                        # List port contexts
ausearch -m AVC -ts recent             # Search SELinux denials

# AppArmor (Ubuntu/Debian)
apparmor_status                         # Show status
sudo aa-status                          # Detailed status
sudo aa-enforce /etc/apparmor.d/profile # Set enforce mode
sudo aa-complain /etc/apparmor.d/profile # Set complain mode
sudo aa-disable /etc/apparmor.d/profile # Disable profile
```

### Password & Account Security

```bash
# chage — password aging policy
chage -l alice                          # List password info
chage -M 90 alice                       # Max password age 90 days
chage -m 7 alice                        # Min days before change
chage -W 14 alice                       # Warn 14 days before expiry
chage -E 2024-12-31 alice               # Account expiry date
chage -d 0 alice                        # Force password change on login

# faillock — account lockout (PAM)
faillock                                # Show failed attempts
faillock --user alice                   # Specific user
faillock --user alice --reset           # Reset failed count
```

### Network Security

```bash
# Check listening services
ss -tulnp                               # All listening services
netstat -tulnp                          # Legacy alternative

# Check open ports
nmap -sT localhost                       # TCP scan
nmap -sU localhost                       # UDP scan

# Check for rootkits
sudo apt install rkhunter chkrootkit
sudo rkhunter --check                   # Run rkhunter
sudo chkrootkit                         # Run chkrootkit

# Intrusion detection (aide)
sudo apt install aide
sudo aide --init                        # Initialize database
sudo aide --check                       # Check for changes
```

---

## 20. Redirection, Pipes & Special Operators

### I/O Redirection

```bash
# Standard streams
# STDIN  = 0 (keyboard input)
# STDOUT = 1 (screen output)
# STDERR = 2 (screen error)

# Redirect STDOUT to file
command > file.txt           # Overwrite
command >> file.txt          # Append

# Redirect STDERR to file
command 2> error.log         # Overwrite errors
command 2>> error.log        # Append errors

# Redirect both STDOUT and STDERR
command > output.txt 2>&1    # Both to same file
command &> output.txt        # Shorthand (bash)
command >> output.txt 2>&1   # Append both

# Discard output
command > /dev/null          # Discard STDOUT
command 2> /dev/null         # Discard STDERR
command &> /dev/null         # Discard all output

# Redirect STDIN
command < input.txt          # Read from file
command << EOF               # Here-document
line1
line2
EOF

# Here string
grep "pattern" <<< "search this string"
```

### Pipes & Command Chaining

```bash
# Pipe — send output of one command as input to another
command1 | command2
ls -la | grep ".txt"
cat /etc/passwd | grep root | cut -d: -f1,3

# Command chaining operators
cmd1 ; cmd2          # Run cmd2 regardless of cmd1 result
cmd1 && cmd2         # Run cmd2 ONLY if cmd1 succeeds (AND)
cmd1 || cmd2         # Run cmd2 ONLY if cmd1 fails (OR)

# Examples
mkdir /tmp/test && cd /tmp/test    # Create and enter dir
apt update || echo "Update failed" # Error handling
./build.sh && ./test.sh && echo "Success"

# Process substitution
diff <(sort file1.txt) <(sort file2.txt)   # Compare sorted output
cat <(ls /tmp) <(ls /var)                  # Combine outputs
```

### Special Characters & Globbing

```bash
# Wildcards (Glob patterns)
*        # Match any string (zero or more chars)
?        # Match any single character
[abc]    # Match any character in set
[a-z]    # Match any character in range
[!abc]   # Match any character NOT in set

# Examples
ls *.txt                # All .txt files
ls file?.txt            # file1.txt, file2.txt, etc.
ls file[123].txt        # file1.txt, file2.txt, file3.txt
rm log[0-9][0-9].txt    # Remove log00.txt to log99.txt

# Brace expansion
mkdir {jan,feb,mar,apr}   # Create 4 dirs
touch file{1..5}.txt       # Create file1.txt to file5.txt
echo {a..z}                # Print alphabet
cp file.txt{,.bak}         # Backup: copy to file.txt.bak

# Command substitution
echo "Today is $(date)"
FILES=$(ls /tmp)
KERNEL=$(uname -r)
```

---

## 21. Shell Scripting Essentials

### Script Structure

```bash
#!/bin/bash
# Script description
# Author: Name
# Date: 2024-01-01
# Usage: ./script.sh [options]

set -e        # Exit on any error
set -u        # Treat unset variables as errors
set -o pipefail  # Pipe fails if any command fails

# Constants
readonly LOG_FILE="/var/log/myscript.log"
readonly MAX_RETRIES=3

# Main logic here
echo "Script started at $(date)"
```

### Variables & User Input

```bash
# Variables
NAME="World"
echo "Hello, ${NAME}!"

# Read user input
read -p "Enter username: " USERNAME
read -sp "Enter password: " PASSWORD   # Silent (no echo)
echo ""
echo "User: $USERNAME"

# Command line arguments
$0        # Script name
$1, $2    # First, second argument
$@        # All arguments as separate words
$*        # All arguments as single word
$#        # Number of arguments
$?        # Exit status of last command
$$        # PID of current script
```

### Conditionals

```bash
# if/elif/else
if [ condition ]; then
    commands
elif [ condition ]; then
    commands
else
    commands
fi

# String tests
if [ "$var" == "value" ]; then ...    # Equal
if [ "$var" != "value" ]; then ...    # Not equal
if [ -z "$var" ]; then ...            # Empty string
if [ -n "$var" ]; then ...            # Non-empty string

# Numeric tests
if [ $a -eq $b ]; then ...   # Equal
if [ $a -ne $b ]; then ...   # Not equal
if [ $a -lt $b ]; then ...   # Less than
if [ $a -le $b ]; then ...   # Less than or equal
if [ $a -gt $b ]; then ...   # Greater than
if [ $a -ge $b ]; then ...   # Greater than or equal

# File tests
if [ -f file ]; then ...     # Is regular file
if [ -d dir ]; then ...      # Is directory
if [ -e path ]; then ...     # Exists
if [ -r file ]; then ...     # Readable
if [ -w file ]; then ...     # Writable
if [ -x file ]; then ...     # Executable
if [ -s file ]; then ...     # Non-empty file
if [ -L link ]; then ...     # Is symbolic link

# Logical operators
if [ cond1 ] && [ cond2 ]; then ...   # AND
if [ cond1 ] || [ cond2 ]; then ...   # OR
if ! [ condition ]; then ...           # NOT

# case statement
case "$variable" in
    "start")
        echo "Starting..."
        ;;
    "stop")
        echo "Stopping..."
        ;;
    *)
        echo "Unknown option"
        ;;
esac
```

### Loops

```bash
# for loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# for loop with range
for i in {1..10}; do
    echo "$i"
done

# for loop with step
for i in {0..20..2}; do     # 0, 2, 4, ..., 20
    echo "$i"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "$i"
done

# for loop over files
for file in /etc/*.conf; do
    echo "Config: $file"
done

# for loop over array
fruits=("apple" "banana" "cherry")
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# while loop
count=0
while [ $count -lt 5 ]; do
    echo "Count: $count"
    ((count++))
done

# until loop (run until condition is true)
until [ $count -ge 5 ]; do
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

# Loop control
break       # Exit loop
continue    # Skip to next iteration
```

### Functions

```bash
# Function definition
function greet() {
    local name=$1       # local variable
    echo "Hello, $name!"
    return 0
}

# Call function
greet "Alice"

# Function with return value
calculate() {
    local result=$(( $1 + $2 ))
    echo $result          # return via stdout
}

sum=$(calculate 5 3)
echo "Sum: $sum"

# Error handling function
log_error() {
    echo "[ERROR] $(date '+%Y-%m-%d %H:%M:%S') - $1" >&2
}

log_info() {
    echo "[INFO]  $(date '+%Y-%m-%d %H:%M:%S') - $1"
}
```

### Arrays

```bash
# Indexed arrays
fruits=("apple" "banana" "cherry")
fruits[3]="date"
echo ${fruits[0]}          # First element
echo ${fruits[@]}          # All elements
echo ${#fruits[@]}         # Array length
echo ${fruits[@]:1:2}      # Slice (index 1, length 2)
unset fruits[1]            # Remove element

# Associative arrays (bash 4+)
declare -A user
user[name]="Alice"
user[age]=30
echo ${user[name]}
echo ${!user[@]}           # All keys
echo ${user[@]}            # All values
```

### Error Handling

```bash
#!/bin/bash
set -euo pipefail

# Trap for cleanup on exit
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
}
trap cleanup EXIT

# Trap for errors
error_handler() {
    echo "Error on line $1"
    exit 1
}
trap 'error_handler $LINENO' ERR

# Check command success
if ! command -v nginx &>/dev/null; then
    echo "nginx is not installed"
    exit 1
fi

# Exit codes
exit 0    # Success
exit 1    # General error
exit 2    # Misuse of shell command
```

---

## 22. Common Interview Questions — Linux

### Q1: What is the difference between a hard link and a soft (symbolic) link?

| Feature | Hard Link | Soft (Symbolic) Link |
|---|---|---|
| Points to | Same inode (data blocks) | File path (name) |
| Cross-filesystem | ❌ No | ✅ Yes |
| Works if original deleted | ✅ Yes (data still accessible) | ❌ No (becomes dangling) |
| Can link directories | ❌ No (usually) | ✅ Yes |
| Size | Same as original | Small (just path string) |
| Command | `ln file link` | `ln -s file link` |

### Q2: Explain file permission 755, 644, 600

```
755 = rwxr-xr-x
  → Owner: read, write, execute
  → Group: read, execute
  → Others: read, execute
  → Use: Directories, executable scripts

644 = rw-r--r--
  → Owner: read, write
  → Group: read only
  → Others: read only
  → Use: Regular files, config files

600 = rw-------
  → Owner: read, write
  → Group: none
  → Others: none
  → Use: Private keys, sensitive config (e.g., ~/.ssh/id_rsa)
```

### Q3: What is the difference between ps aux and ps -ef?

```
ps aux  → BSD syntax
  a = show processes for all users
  u = display user-oriented format
  x = include processes without terminal

ps -ef  → UNIX/SysV syntax
  -e = show all processes
  -f = full format listing

Both show all processes; main difference is output format columns.
```

### Q4: How does the boot process work in Linux?

```
1. BIOS/UEFI      → POST, find bootable device
2. MBR/GPT        → Load bootloader from disk
3. GRUB           → Load kernel + initrd into memory
4. Kernel Init    → Mount root filesystem, start kernel
5. initramfs      → Temporary root fs for early boot
6. init / systemd → PID 1; start system services
7. Login          → TTY / Display Manager
```

### Q5: What is a zombie process?

A **zombie process** is a process that has completed execution but still has an entry in the process table because the parent hasn't read its exit status using `wait()`.

```bash
# Find zombie processes
ps aux | grep Z
ps -el | grep 'Z'
top   # Look for 'Z' in STATUS column
```

### Q6: Difference between /etc/passwd and /etc/shadow

| /etc/passwd | /etc/shadow |
|---|---|
| World-readable (644) | Root-readable only (640) |
| Contains username, UID, GID, home, shell | Contains encrypted password + aging info |
| `x` placeholder in password field | Actual password hash |

### Q7: What is a runlevel? (SysV vs systemd)

| Runlevel | SysV Meaning | systemd Target |
|---|---|---|
| 0 | Halt | poweroff.target |
| 1 | Single user mode | rescue.target |
| 2 | Multi-user, no NFS | multi-user.target |
| 3 | Full multi-user (CLI) | multi-user.target |
| 4 | Unused | — |
| 5 | Multi-user with GUI | graphical.target |
| 6 | Reboot | reboot.target |

### Q8: What is the difference between kill -9 and kill -15?

| `kill -15` (SIGTERM) | `kill -9` (SIGKILL) |
|---|---|
| Graceful termination request | Immediate force kill |
| Process can catch and handle | Cannot be caught or ignored |
| Allows cleanup actions | No cleanup, may corrupt data |
| Should be tried first | Use only if SIGTERM fails |

### Q9: How do you check which process is using a specific port?

```bash
ss -tulnp | grep :80
lsof -i :80
netstat -tulnp | grep :80
fuser 80/tcp
```

### Q10: What is the difference between `>` and `>>`?

```bash
command > file    # Overwrite (truncate file first, then write)
command >> file   # Append (add to end without erasing existing)
```

### Q11: What does /proc contain?

`/proc` is a virtual pseudo-filesystem that provides an interface to kernel data structures:
- `/proc/cpuinfo` — CPU details
- `/proc/meminfo` — Memory usage
- `/proc/PID/` — Info about specific process (PID)
- `/proc/net/` — Network statistics
- `/proc/sys/` — Tunable kernel parameters (via `sysctl`)

### Q12: How to find large files?

```bash
find / -size +100M -type f 2>/dev/null
find / -size +1G -type f 2>/dev/null
du -ah / | sort -rh | head -20
ncdu /
```

---

## 🔧 Quick Reference Cheat Sheet

### Most Used Commands

```bash
# Navigation
pwd | cd | ls -la | tree

# Files
cp | mv | rm -rf | touch | ln -s | stat | find | locate

# Viewing
cat | less | head | tail -f | grep -rn

# Permissions
chmod 755 | chown user:group | umask | ls -la

# Processes
ps aux | top | kill -9 | pgrep | jobs | nohup

# Networking
ip a | ss -tulnp | ping | curl | wget | nmap | ssh

# System
df -h | free -h | uname -a | uptime | dmesg -T

# Services
systemctl start|stop|restart|enable|status

# Packages
apt update && apt upgrade | apt install | apt remove

# Archives
tar -czvf archive.tar.gz dir/ | tar -xzvf archive.tar.gz

# Text processing
grep | sed | awk | cut | sort | uniq | wc | tr

# Disk
df -h | du -sh * | lsblk | fdisk -l | mount
```

---

*📌 This guide is part of the Interview Preparation repository.*
*Last Updated: May 2026*