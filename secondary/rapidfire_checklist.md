# Rapid Fire Checklist

## Create a new droplet/server node
- [ ] Create a new droplet on digital ocean / linode
- [ ] Don't use SSH for root login. Setup only password. (Since later in settings, no root login /SSH is allowed)

## Login as root
- [ ] ```sudo adduser ash``` Create a new user
- [ ] ```sudo usermod -aG sudo ash``` Add ash to sudo group 
- [ ] ```getent group sudo``` Check ash is in list of sudo-ers
- [ ] ```ssh-keygen -t ed25519``` Setup ssh for ash using local workstation
- [ ] Add public SSH key to ~/.ssh/authorised keys in new remote server
- [ ] Setup .ssh/config in local workstation
- [ ] Add identity file in local workstation 
  - ```eval $(ssh-agent)```
  - ```ssh-add ~/.ssh/id_rsa```

## Login in ```ash```
- [ ] ```mkdir .ssh``` Create a new directory
- [ ] ```touch .ssh/authorized_keys``` Add your public key from the local machine to this file

## Update and upgrade
- [ ] sudo apt update
- [ ] sudo apt upgrade

## Install packages
- [ ] sudo apt install htop tmux lvm2 mariadb-server fail2ban ufw ncdu 
- [ ] install docker: ```https://docs.docker.com/engine/install/ubuntu/ ```

## Secure the server
- [ ] DON’T LOGOUT! Test that you can SSH from another terminal after every change.
- [ ] Change port number : 
    - [ ] ```sudo nano /etc/ssh/sshd_config```
    - [ ] ```sudo nano /etc/systemd/system/sockets.target.wants/ssh.socket```
    - [ ] ```sudo systemctl daemon-reload```
    - [ ] ```sudo systemctl restart ssh```
- [ ] Allow SSH logins only for specific users
    - [ ] ```sudo nano /etc/ssh/sshd_config```
    - [ ] ```AllowUsers user1 user2```
    - [ ] ```sudo systemctl restart ssh```
    - [ ] Test with root user (should not be able to SSH anymore)
- [ ] Don't permit Root login via SSH: 
    - [ ] ```sudo nano /etc/ssh/sshd_config```
    - [ ] ```PermitRootLogin yes``` to ```PermitRootLogin no```
- [ ] Password Authentication
    - [ ] Ensure that ```.ssh/authorized_keys``` exist in user ash
    - [ ] ```sudo nano /etc/ssh/sshd_config```
    - [ ] ```PasswordAuthentication yes``` to ```PasswordAuthentication no```
    - [ ] ```sudo nano /etc/ssh/sshd_config.d/*.conf```
    - [ ] ```PasswordAuthentication yes``` to ```PasswordAuthentication no```
    - [ ] ```sudo systemctl restart ssh```
    - [ ] Test connection
    - [ ] REMEMBER: To add ```.ssh/authorized_keys``` to used ash’s .ssh folder!!!!!
- [ ] Install and configure fail2ban
- [ ] Configure ```/etc/hosts.allow``` & ```/etc/hosts.deny```: with home, office, and DO IPs (Especially if it is a database server)
    - [ ] ```sudo nano /etc/hosts.allow ```
        - [ ] ALL: 192.168.1.50
    - [ ] ```sudo nano /etc/hosts.deny```
        - [ ] ALL: ALL
- [ ] Setup a firewall using Uncomplicated Firewall (UFW) 

## Check logs on security
- [ ] ```sudo last```: provides a list of all users and terminals that have logged into the server. reads data from the /var/log/wtmp file.
- [ ] ```sudo lastb```: The lastb command shows the bad login attempts.
- [ ] ```grep 'Failed password' /var/log/auth.log``` : All authentication related events, successful or not, are logged in /var/log/auth.log file
- [ ] ```grep 'Accepted password' /var/log/auth.log```: All authentication related events, successful or not, are logged in /var/log/auth.log file
- [ ] ```lastlog``` : displays the details of the last login of all users or of a given user. It's important to note that this command doesn't show any IP addresses
- [ ] ```sudo cat /var/log/fail2ban.log``` : Fail2Ban logs its actions here

## Mount & manage storage volumes

## Configure new mariadb server location.
