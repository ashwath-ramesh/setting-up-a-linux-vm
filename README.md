# setting-up-a-linux-vm
# Setting up a linux virtual machine to run scripts

## 1. Create the linux virtual machine
- apt udpate
- apt upgrade
- create a non root user
- If you need to rename the linux server:
  - Use the hostnamectl command to view the current hostname of the system
  - Use the command to set the new hostname of the system. hostnamectl set-hostname <new_hostname>

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

## Note on SSH keys
### Generating a key
- ssh-keygen
- ssh-keygen -t rsa -b 4096 -C "my_comment"

### Logging into a remote server using a specific private key
- ssh -i ~/.ssh/id_rsa root@123.456.789.012
