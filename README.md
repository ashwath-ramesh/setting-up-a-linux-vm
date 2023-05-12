# Setting up a linux virtual machine

Make a copy of this checklist, and use it to install and deploy your linux virtual machine.

Note: 
- This document is intended for my personal use to streamline my personal workflow. This document is not intended to be a comprehensive guide to linux administration. 
- This is a Work In Progress (WIP) document.
- Any IP addresses, usernames, passwords, etc. are examples only.
- Overall structure/outline for this checklist is based on the excellent book 'Mastering Ubuntu Server' by Jay LaCroix. But contents are populated using multiple linux reference books mentioned at the end of this document.

## CHECKLIST

### 1. Deploy the server

- [ ] Distribution: Ubuntu 22.10
- [ ] Confirm minimum requirements: 64-bit CPU, 1 GB RAM, 10-GB hard disk (> 16 GB recommended))
- [ ] Confirm role: Database server, file server, web server ...
- Is the system mission-critical? 
  - [ ] Yes. Plan backup and recovery.
  - [ ] No
- How confidential is the data?
  - [ ] 1:low
  - [ ] 5:high. Enable encryption at rest and at transit.
- Choose a device for the server: 
  - [ ] Physical server
  - [ ] Desktop
  - [ ] Laptop
  - [ ] Virtual machine
  - [ ] Virtual private server
  - [ ] Raspberry Pi

### 2. Manage users and permissions 

1. Manage users
   1. Create a user account
      1. [ ] Create a user: ```sudo adduser <username>```
      2. [ ] Confirm user has been created: ```cat /etc/passwd``` & ```sudo cat /etc/shadow```
      3. [ ] Login as user (with user's password): ```su - <username>``` (or) Login as user (with your password): ```sudo su - <username>```
      4. [ ] Ensure that the user directory exists in ```/home/<username>```
      5. [ ] Exit from <username>: ```exit```
   2. Remove a user
      1. [ ] Delete the user: ```sudo userdel <username>``` (Does **not** remove contents of users' home directory)
      2. [ ] Move contents of users home directory to archives: ```sudo mv /home/<username> /store/file_archive```
   3. Distribute a default configuration file (/etc/skel)
      1. [ ] View contents of directory: ```ls -al /etc/skel```
      2. [ ] Add content .... 
2. Manage groups
   1. View all groups in server: ```cat /etc/group```
   2. View groups associated with a user: ```groups <username>```
   3. Create a new group: ```sudo groupadd <groupname>```
   4. Delete a group: ```sudo groupdel <groupname>```
   5. Add user to a group: ```sudo gpasswd -a <username> <groupname>``` (or) ```sudo usermod -aG <groupname> <username>```
   6. Remove user from a group: ```sudo gpasswd -d <username> <groupname>```
   7. Change a user's primary group: ```sudo usermod -g <groupname> <username>```
   8. Change a user's primary directory: 
      1. Move old home dir to new home dir: ```sudo usermod -d <new_dir_path> <old_username>```
      2. Change the username: ```sudo usermod -l <new_username> <old_username>```
3. Manage password and password policies
   1. Lock a user account
   2. Unlock a user account
   3. Check when users password was last changed: ```sudo passwd -S <username>```
   4. Set password expiration information
   4. Set a password policy
4. Configure administrator access with sudo
   1. Add users to the sudo group using ```sudo usermod -aG sudo <username>```
   2. Add sudo to users using ```visudo``` (edit: /etc/sudoers)
      1. Users is able to use sudo from which terminals: xxx 
      2. User is able to impersonate which users: xxx 
      3. User is able to impersonate which groups: xxx
      4. User is able to run which commands: xxx
5. Set permission on files and directories
   1. Change permissions (using octal notations): ```chmod [options] <permissions> <file>``` (Ref: ```https://quickref.me/chmod```) 
      1. on a file: ```chmod 755 foo.txt```
      2. on a directory: ```chmod -R 755 my_directory```
   2. Change ownership:
      1. To a new user only:
         1. of a file: ```chown <to_user> <filename>```
         2. of a directory (and all its files & dirs): ```chown -R <to_user> <dir>```
      2. To a new group only:
         1. of a file: ```chgrp <to_group> <filename>```
         2. of a directory (and all its files & dirs): ```chgrp -R <to_group> <dir>```
      3. To a new user & new group: ```chown <to_user>:<to_group> <filename>```

### 3. Manage software packages

1. Search for a package
2. Install a package
3. Remove a package
4. Manage package repositories
5. Backup Debian packages
   1. [ ] Get a list of installed packages
   2. [ ] Update the index of available packages
   3. [ ] Ensure we have ```dselect``` package installed
   4. [ ] Import previously saved package list and have the missing packages installed on the server
5. Clean up orphaned packages

### 4. Manage processes

1. View jobs: ```jobs```
2. Move to background: ```<command> &```
3. Move to foreground: ```fg <job_number>```
4. View running processes:
   1. All: ```ps aux```
   2. Specific process: ```ps aux  | grep <process_name>```
   3. Top 5 high CPU consuming processes: ```ps aux --sort=-pcpu | head -n 5```
   4. Top 5 high Memory consuming processes: ```ps aux --sort=-pmem | head -n 5```
5. Change priority of processes 
6. Deal with misbehaving processes
   1. Kill process gracefully: ```kill -15 <pid>```
   2. Kill process forcefully: ```kill -9 <pid>```
   3. Kill all process by name: ```killall <process_name>```
7. Manage system processes
   1. Find system process: ```systemctl | grep <process_name>```
   2. Check status: ```systemctl status <process_name>```
   3. Start a process: ```systemctl start <process_name>```
   4. Stop a process: ```systemctl stop <process_name>```
   5. Enable a process: ```systemctl enable <process_name>```
   6. Disable a process: ```systemctl disable <process_name>```
   7. Restart a process: ```systemctl restart <process_name>```
8. Schedule tasks
   1. Edit: ```crontab -e```
   2. View: ```crontab -l```

### 5. Monitoring system resources

1. Disk usage 
   1. Overview
      1. ```df -h```
      2. ```df -i```
   2. Disk usage by directory (Directory permissions matters ...)
      1. Manual traversing & checking:
         1. ```du -hc``` (or) ```du -hsc``` (h: human readable, s: summary, c: cumulative total space used) 
         2. ```du -h --max-depth=1```
         3. ```du -h --max-depth=1 | sort -h```
      2. Using ```ncdu``` (ncurses disk usage)
         1. ```sudo apt install ncdu```
         2. ```ncdu```
2. Memory usage 
   1. Check column 'additional': ```free -m``` (or) ```free```
   2. Managing swap 
3. Load average
   1. View load average (1-5-15 mins): ```uptime``` (or) ```cat /proc/loadavg```
4. Resource usage
   1. ```sudo apt install htop```
   2. ```htop```

### 6. Manage storage volumes
  
Managing volumes guide: https://github.com/ashwath-ramesh/managing-volumes-on-an-ubuntu-server/blob/main/mount-manage-storage.md

Setting up LVM for Filesystem & MariaDB: https://github.com/ashwath-ramesh/managing-volumes-on-an-ubuntu-server/blob/main/utilize-lvm.md

1. Add additional storage volumes
   1. When adding additional storage to your system, you should ask yourself the following questions:
      1. How much storage do you need?
      2. After you attached it, what device name did it receive? 
         1. ```sudo fdisk -l``` Use before and after attaching the storage to see the difference.
         2. ```dmesg --follow```, attach disk, watch output. When done, press Ctrl + c on your keyboard to return to the prompt.
         3. ```lsblk```
      3. How do you want the storage device formatted?
      4. Where do you want it mounted?
2. Format and partition storage devices
   1. Create an actual partition on the device: 
      1. ```sudo fdisk <storage device name>```, e.g: ```sudo fdisk /dev/sdb```
      2. Which one: Master Boot Record (MBR) ```o``` and GUID Partition Table (GPT) partition tables ```g```?
      3. Type ```n``` to tell fdisk that you would like to create a new partition
      4. Then, you’ll be asked if you would like to create a primary or extended partition (if you’ve opted for MBR). With MBR, you would want to choose primary for the first partition, and then you can use extended for creating additional partitions. If you’ve opted for GPT, this prompt won’t appear, as it will create your partition as primary no matter what.
      5. Partition number: use default
   2. Format the partition
      1. ```sudo mkfs.<filesystem type> <storage device name>``` E.g: ```sudo mkfs.ext4 /dev/sdb1``` (recommended by default) (or) ```sudo mkfs.xfs /dev/sdb1```
3. Mount and unmount volumes
   1. ```sudo mkdir /mnt/vol1```
   2. ```sudo mount /dev/sdb1 /mnt/vol1``` (or) ```sudo mount /dev/sdb1 -t ext4 /mnt/vol1```
   3. In order to ensure the mount is available anytime your server boots up, you’ll need to edit the ```/etc/fstab``` file
4. /etc/fstab
5. Backup and restoring volumes
6. Utilize LVM
   1. Starting with LVM
      1. ```apt search lvm2 |grep installed```
      2. ```sudo apt install lvm2```
   2. Format logical volumes
   3. Removing volumes with LVM
   4. LVM snapshots

### 7. Connect to networks

1. Set the hostname
2. Manage the network interface
3. Assign a static IP address
4. OpenSSH & key management
   1. Install OpenSSH
   2. Ensure that the OpenSSH service is running
   2. Generate public and private keys
      1. Goto: ```/home/user/.ssh``` folder 
      2. ```ssh-keygen``` or ```ssh-keygen -t rsa -b 4096``` or ```ssh-keygen -t rsa -b 4096 -C "my_comment"```
      3. ```ssh-keygen -t ed25519```
   3. Copying public keys to remote servers: ```ssh-copy-id -p 2222 -i ~/.ssh/id_rsa.pub username@192.168.1.150```
   4. Utilizing an SSH agent: 
      1. Start the agent on the local machine: ```eval $(ssh-agent)```
      2. Give the path to the private key: ```ssh-add ~/.ssh/id_rsa```
      3. Save catchphrase when asked for it. 
      4. Connect to the remote server. No more passphrase required.
   5. Change the passphrase of an OpenSSH key
      1. ```ssh-keygen -p```
      2. Then enter the directory path to the private key
      3. Enter new catchphrase
   6. Simplify SSH connections with a config file
      1. Go to local machine config file: ```nano /home/user/.ssh/config```
      2. Edit file with Hostname, Host, Port, User, ...
   7. Disable password based authentication

### 8. Setup network services

1. Plan your IP address scheme
2. Setup a DHCP server for serving IP addresses
3. Add a DNS server
4. Setup an internet gateway

### 9. Share and transfer files

1. Setup NFS share
2. Transfer files with ```rsync```
   1. Copy contents of a directory to another directory: ```rsync -avr <source_dir> <destination_dir>```
   2. Copy over SSH to another server. Variations:
      1. ```rsync -avz -e 'ssh config_hostname' :<source_dir> <destination_dir>```
      2. ```rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine```
3. Transfer files with ```scp```

### 10. Manage databases

**Pre-requisite:** Use Logical Volume Manager (LVM) for the partition that houses your database files.

1. Install MariaDB: 
   1. ```sudo apt install mariadb-server```
   2. Check server is enabled: ```systemctl status mariadb```
   3. Setup security: ```sudo mysql_secure_installation```
      1. Enter current password for root (enter for none): ```<enter>```
      2. Switch to unix_socket authentication [Y/n] n
      3. Change the root password? [Y/n] Y
      4. Remove anonymous users? [Y/n] Y
      5. Disallow root login remotely? [Y/n] Y
      6. Remove test database and access to it? [Y/n] Y
      7. Reload privilege tables now? [Y/n] Y
   4. Start mariadb: No need to enter root pwd ```sudo mariadb```  (or) Enter root pwd ```mariadb -u root -p```
2. Update the MariaDB configuration files
   1. Directory: ```/etc/mysql```
   2. Config file that mariadb reads on startup: ```cat /etc/mysql/mariadb.cnf```
   3. Set default values for mariadb when it starts: ```cat /etc/mysql/debian-start```
   4. Setup primary & secondary server relationships: ```cat /etc/mysql/conf.d/mysql.cnf```
3. Manage the MariaDB database
   1. Basic setup (Define what users are going to do; principal of least privileges):
      1. root -> Manages users (and everything else)
      2. admin -> Manages database (but not user setup)
      3. readonlyuser -> Can only read database
   2. Setup ```admin``` and ```readonlyuser```:
      1. Log in as root: ```sudo mariadb```
      2. Setup admin (can only log in to the DB after first logging in to the local server)
         1. Based on log in to the DB from location: 
            1. From locahost (Most secure): ```CREATE USER 'admin'@'localhost' IDENTIFIED BY 'password';```
            2. From anywhere (Least secure): ```CREATE USER 'admin'@'%' IDENTIFIED BY 'password';```
            3. From IP address: ```CREATE USER 'admin'@'192.168.1.%' IDENTIFIED BY 'password';```
         2. ```FLUSH PRIVILEGES;```
      3. Grant full permissions to admin user:
         1. Grant full access to DB: ```GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost';```
         2. ```FLUSH PRIVILEGES;```
      4. Grant read-only permissions to ```readonlyuser```:
         1. Access to all databases (not recommended): ```GRANT SELECT ON *.* TO 'readonlyuser'@'localhost' IDENTIFIED BY 'password';```
         2. Access to specific databases (recommended): ```GRANT SELECT ON mysampledb.* TO 'readonlyuser'@'localhost' IDENTIFIED BY 'password';```
         3. ```FLUSH PRIVILEGES;```
      4. Other grants:
         1. All access to a specific DB: ```GRANT ALL ON mysampledb.* TO 'appuser'@'localhost' IDENTIFIED BY 'password';```
         2. ```CREATE```, ```DELETE```, ```SELECT```, ```INSERT```, ```DROP```
      5. Drop user: ```DROP USER 'myuser'@'localhost';```
      6. Check grants: ```SHOW GRANTS FOR 'readonlyuser'@'localhost';```
   3. Create a database
      1. Log in as admin: ```mariadb -u admin -p;```
      2. ```CREATE DATABASE mysampledb;```
      3. ```SHOW DATABASES;```
      4. List users: ```SELECT HOST, USER, PASSWORD FROM mysql.user;```
      5. Change to a database: ```USE mysampledb;```
         1. ```CREATE TABLE Employees (Name char(15), Age int(3), Occupation char(15));```
         2. ```SHOW COLUMNS IN Employees;```
         3. ```INSERT INTO Employees VALUES ('Joe Smith', '26', 'Ninja');```
         4. ```SELECT * FROM Employees;```
         5. ```DELETE FROM Employees WHERE Name = 'Joe Smith';```
         6. ```DROP TABLE Employees;```
         7. ```DROP DATABASE mysampledb;```
   4. Backup a database (from Linux terminal): ```mysqldump -u admin -p --databases mysampledb > mysampledb.sql;``` (or) ```mysqldump -u admin -p --databases mysampledb > /path/to/output/mysampledb.sql;```
4. Setup a secondary database server

### 11. Secure the server

1. Lower the attack surface
2. Respond to CVEs
3. Automatically installing patches with Canonical Livepatch service
4. Install security updates
5. Securing OpenSSH
   1. All changes to be made in file: ```sudo nano /etc/ssh/sshd_config```
   2. Change the port number on which OpenSSH listens
      1. Change line ```Port 22 to Port 2222```
      2. Restart: ```sudo systemctl restart ssh```
      3. From Ubunto 22.10 Important note: https://discourse.ubuntu.com/t/sshd-now-uses-socket-based-activation-ubuntu-22-10-and-later/30189/9
         1. sudo mkdir -p /etc/systemd/system/ssh.socket.d
         2. sudo nano /etc/systemd/system/ssh.socket.d/listen.conf 
         3. Add the following lines to the file:
            1. [Socket]
            2. ListenStream=
            3. ListenStream=2222
         4. sudo service ssh reload
   2. Change to Protocol 2 from Protocol 1 (only for older Linux versions)
      1. Change line ```Protocol 1 to Protocol 2```
      2. Restart: ```sudo systemctl restart ssh```
   3. Allow SSH logins only for specific users
      1. First create a new group: ```sudo groupadd sshusers```
      2. Then make one or more users members of that group: ```sudo usermod -aG sshusers <myuser>```
      3. Goto file: ```sudo nano /etc/ssh/sshd_config```
      4. ```AllowUsers user1 user2``` 
   4. Don't permit Root login via SSH
      1. Change: PermitRootLogin yes to PermitRootLogin no
      2. Restart: ```sudo systemctl restart ssh```
   5. Don't allow password authentication
      1. Change: PasswordAuthentication yes to PasswordAuthentication no
      2. Restart: ```sudo systemctl restart ssh```
      3. Sometimes the sshd/config file contains: ```Include /etc/ssh/sshd_config.d/*.conf``` 
         1. Goto: ```sudo nano /etc/ssh/sshd_config.d/*.conf```
         2. Change: PasswordAuthentication yes to PasswordAuthentication no
         3. Restart: ```sudo systemctl restart ssh```
6. Install and configure Fail2ban
7. MariaDB best practices for secure database servers
8. Setup a firewall
9. Encrypt and decrypt disks using LUKS
10. Locking down SUDO

### 12. Troubleshooting

1. View system logs
2. Trace network issues
3. Troubleshoot resource issues
4. Diagnosing defective RAM

### 13. Prevent disasters

1. Utilize git for configuration management
2. Implement a backup plan

### Reference books
1. Mastering Ubuntu Server: Master the art of deploying, configuring, managing, and troubleshooting Ubuntu Server 18.04, 2nd Edition. Jay LaCroix
2. The Linux Command Line, 2nd Edition: A Complete Introduction. William E. Shotts, Jr.
3. Mastering Linux Security and Hardening: Protect your Linux systems from intruders, malware attacks, and other cyber threats, 2nd Edition. Donald A. Tevault
4. How Linux Works, 3rd Edition: What Every Superuser Should Know 3rd Edition. Brian Ward

--- 

# SECTION TO BE ARCHIVED ONCE INCORPORATED INTO THE ABOVE CHECKLIST

## 1. Create the linux virtual machine / Deploy the server
- Ensure you have SSH key authentication for root user. See below.
- apt update
- apt upgrade

- **create a non-root user**
  - adduser john
  - confirm user created/check list of users: cut -d: -f1 /etc/passwd (or) cat /etc/passwd
  - login as user john: su john
  - ensure that the users directory exists in /home/john/
  - exit from john to root: exit

- **give john (and root) SSH access**
  - As root user go to: sudo nano /etc/ssh/sshd_config
  - Go to bottom of file/modify new lines, save and exit: AllowUsers john root
  - systemctl restart ssh (or) systemctl restart sshd
  - exit
  - ssh john@xxx.xxx.xxx.xxx

- Ensure that john has an authorized_keys file in the .ssh directory
  - From root user account, go to: /home/john
  - mkdir .ssh
  - touch authorized_keys
  - nano authorized_keys
  - Save a new SSH key that belongs to John on the local and remote machine. Save the public key in this file.

**To add non-root user as sudo-er**
- goto root user account
- Add user john to group sudo: 'usermod -aG sudo john'
- Check what groups user john belongs to: 'groups john'
- Now exit from root user. Exit from john.
- Relogin as john
- 'sudo whoami'
- Enter password
- Output should be 'root'

- **If you need to rename the linux server:**
  - Use the hostnamectl command to view the current hostname of the system
  - Use the command to set the new hostname of the system. hostnamectl set-hostname <new_hostname>

**[INCOMPLETE] How to disable SSH for root user**
- root user can only access terminal through normal user entry, then su -
- Now make sure that

- reboot

## 2. Install python & libraries
- check python3 --version
- apt install python3-pip
- cd ..
- cd /home

## 3. Git clone repository using SSH
- Login as non-root user!
- Generate an SSH key pair on the Linux virtual machine: ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
- Add your public SSH key to your GitHub account. You can do this by going to your GitHub account settings, and selecting "SSH and GPG keys" from the left-hand menu. Click the "New SSH key" button, and paste your public SSH key into the "Key" field.
- Clone the repository using the git clone command, specifying the SSH URL of the repository: 
  - git clone -i ~/.ssh/id_rsa git@github.com:USERNAME/REPOSITORY.git
  - If you have multiple SSH keys associated with your GitHub account, you may need to specify which key to use when cloning the repository. You can do this using the -i flag and specifying the path to the private key file
  - If you are unable to find the .ssh folder using the ls command, it is possible that it has not been created yet. You can create the .ssh folder using the following command: mkdir ~/.ssh
  - If you get error error: unknown switch `i', then use the following command instead: GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa' git clone git@github.com:USERNAME/REPOSITORY.git

## 4. Install venv
- Create a venv: python3 -m venv myenv (or) apt install python3.10-venv
- Activate the venv: source myenv/bin/activate

pip install virtualenv
virtualenv myenv
source myenv/bin/activate



## 5. Install python libraries
- use the pip freeze command to create a requirements.txt file from the packages installed in your current environment. 
- Run command: pip freeze > requirements.txt 
- pip install requests beautifulsoup3 pandas (or) pip install -r requirements.txt

**If you need sqlite3**
sudo apt install sqlite3

## Note on Authentication (SSH keys & passwords)
### SSH Keys
#### Generating an SSH key
- ssh-keygen (or)
- ssh-keygen -t rsa -b 4096 -C "my_comment"

#### Logging into a remote server using a specific private key
- ssh -i ~/.ssh/id_rsa root@123.456.789.012

#### Logging from a remote server into GitHub using a specific private key
- Test connection with: ssh -T git@github.com -i ~/.ssh/my_private_key

#### Adding in new public SSH keys to the vm
- go to: nano ~/.ssh/authorized_keys
- add the public key in a new line
  
#### Git clone a repo to vm
  - Create an SSH key on local virtual machine
  - Add that SSH key to your deploy key in the repo
  - Test that ssh keys works using: ```ssh -T git@github.com -i ~/.ssh/my_private_key```
  - Create a config file input
  - - ```nano ~/.ssh/config```
  - - Add text below
```
  Host your_custom_hostname
    HostName github.com
    User git
    IdentityFile /path/to/your/private_key
 ```
  - From the folder in which you want your repo: ```git clone git@your_custom_hostname:user/repo.git```
