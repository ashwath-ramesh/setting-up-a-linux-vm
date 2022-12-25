# setting-up-a-linux-vm
# Setting up a linux virtual machine to run scripts

## 1. Create the linux virtual machine
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
