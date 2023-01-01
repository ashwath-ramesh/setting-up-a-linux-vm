# Setting up a linux virtual machine

Make a copy of this checklist, and use it to install and deploy your linux virtual machine.

CHECKLIST
1. Deploy the server
   - [ ] Distribution: Ubuntu
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
2. Manage users and permissions 
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
         4. [ ] View contents of directory: ```ls -al /etc/skel```
         5. [ ] Add content .... 
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
      1. Change permissions on a file (using octal notations)
      2. Change permission on a directory
      3. Change ownership of a file/directory
3. Manage software packages
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
4. Manage processes
   - Manage jobs
   - View running processes
   - Change priority of processes
   - Deal with misbehaving processes
   - Manage system processes
   - Schedule tasks
5. Monitoring system resources
   - Disk usage
   - Memory usage
   - Load average
   - Resource usage
5. Manage storage volumes
   1. Add additional storage volumes
   2. Format and partition storage devices
   3. Mount and unmount volumes
   4. /etc/fstab
   5. Backup and restoring volumes
   6. Utilize LVM
6. Connect to networks
   1. Set the hostname
   2. Manage the network interface
   3. Assign a static IP address
   4. OpenSSH & key management
      1. Install OpenSSH
      2. Ensure that the OpenSSH service is running
      2. Generate public and private keys
      3. Copying public keys to remote servers
      4. Utilizing an SSH agent 
      5. Change the passphrase of an OpenSSH key
      6. Simplify SSH connections with a config file
7. Setup network services
   1. Plan your IP address scheme
   2. Setup a DHCP server for serving IP addresses
   3. Add a DNS server
   4. Setup an internet gateway
8. Share and transfer files
   1. Setup NFS share
   2. Transfer files with ```rsync```
   3. Transfer files with ```scp```
9. Manage databases
   1. MariaDB
      1. Install MariaDB
      2. Update the MariaDB configuration files
      3. Manage the MariaDB database
      4. Setup a secondary database server
10. Secure the server
    1. Lower the attach surface
    2. Respond to CVEs
    3. Automatically installing patches with Canonical Livepatch service
    4. Install security updates
    5. Securing OpenSSH
    6. Install and configure Fail2ban
    7. MariaDB best practices for secure database servers
    8. Setup a firewall
    9. Encrypt and decrypt disks using LUKS
    10. Locking down SUDO
11. Troubleshooting
    1. View system logs
    2. Trace network issues
    3. Troubleshoot resource issues
    4. Diagnosing defective RAM
13. Prevent disasters
    1. Utilize git for configuration management
    2. Implement a backup plan

--- 

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

#### Adding in new public SSH keys to the vm
- go to: nano ~/.ssh/authorized_keys
- add the public key in a new line

### Passwords
#### Logging in using passwords
**God forbid, you want to login using password. Then you have to set this up inside the linux vm (logged in as root user)**
- Open the /etc/ssh/sshd_config file in a text editor.
- Locate the line #PasswordAuthentication yes and remove the # character to uncomment the line.
- Save the changes to the file and exit the text editor.
- Restart the SSH daemon to apply the changes: systemctl restart ssh


References
https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-18-04
