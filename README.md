# setting-up-a-linux-vm
# Setting up a linux virtual machine to run scripts

## 1. Create the linux virtual machine
- Ensure you have SSH key authentication. See below.
- apt update
- apt upgrade
- create a non root user
  - adduser john
  - To add the user to a specific group, use the usermod command. For example, to add the user john to the sudo group, which allows the user to execute commands with superuser privileges, type usermod -aG sudo john.
  - Ensure 'john' has SSH key authentication. Password authentication should be not allowed by default.
- If you need to rename the linux server:
  - Use the hostnamectl command to view the current hostname of the system
  - Use the command to set the new hostname of the system. hostnamectl set-hostname <new_hostname>
- reboot
- List all users on vm: cat /etc/passwd

## 2. Install python & libraries
- check python3 --version
- apt install python3-pip
- cd ..
- cd /home

## 3. Git clone repository using SSH
- Generate an SSH key pair on the Linux virtual machine: ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
- Add your public SSH key to your GitHub account. You can do this by going to your GitHub account settings, and selecting "SSH and GPG keys" from the left-hand menu. Click the "New SSH key" button, and paste your public SSH key into the "Key" field.
- Clone the repository using the git clone command, specifying the SSH URL of the repository: 
  - git clone -i ~/.ssh/id_rsa git@github.com:USERNAME/REPOSITORY.git
  - If you have multiple SSH keys associated with your GitHub account, you may need to specify which key to use when cloning the repository. You can do this using the -i flag and specifying the path to the private key file
  - If you are unable to find the .ssh folder using the ls command, it is possible that it has not been created yet. You can create the .ssh folder using the following command: mkdir ~/.ssh
  - If you get error error: unknown switch `i', then use the following command instead: GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa' git clone git@github.com:USERNAME/REPOSITORY.git

## 4. Install venv
- Create a venv: python3 -m venv myenv
- Activate the venv: source myenv/bin/activate


## 5. Install python libraries
- use the pip freeze command to create a requirements.txt file from the packages installed in your current environment. Run command: pip freeze > requirements.txt (or) pip install requests beautifulsoup3 pandas

## Note on Authentication (SSH keys & passwords)
### SSH Keys
#### Generating an SSH key
- ssh-keygen
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
