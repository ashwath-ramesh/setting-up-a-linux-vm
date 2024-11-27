# Rapid Fire Checklist

## 1. Create a new droplet/server node

- [ ] Create a new droplet on digital ocean / linode
- [ ] Don't use SSH for root login. Setup only password. (Since later in settings, no root login /SSH is allowed)

## 2. Setup SSH keys for login to remote server

- [ ] `ssh-keygen -t ed25519`: In local workstation, setup ssh for ash

## 3. Login as root to remote server

- [ ] `sudo adduser ash`: Create a new user
- [ ] `sudo usermod -aG sudo ash`: Add ash to sudo group
- [ ] `getent group sudo`: Check ash is in list of sudo-ers
- [ ] Switch to user ash: `su - ash`
- [ ] Create the .ssh directory and the authorized_keys file
  - [ ] `mkdir ~/.ssh`
  - [ ] `chmod 700 ~/.ssh`
  - [ ] `touch ~/.ssh/authorized_keys`
  - [ ] `chmod 600 ~/.ssh/authorized_keys`
- [ ] Add public SSH key from local workstation to remote server: `~/.ssh/authorised keys`

## 4. Simplify logging in with local workstation

- [ ] Setup `~/.ssh/config` in local workstation (see format below)
- [ ] Add identity file in local workstation
  - `eval $(ssh-agent)`
  - `ssh-add ~/.ssh/id_rsa`

```
Host your_custom_hostname
    AddKeysToAgent yes
    HostName github.com
    User git
    Port 22  # Default SSH port
    IdentityFile ~/.ssh/private_key
```

## 5. Install packages

- [ ] `sudo apt update && sudo apt upgrade`
- [ ] `sudo apt install htop tmux lvm2 fail2ban ufw ncdu git`
- [ ] install docker: `https://docs.docker.com/engine/install/ubuntu/ `
- [ ] `sudo apt install nginx/apache/caddy`
- [ ] optional: `sudo apt install mariadb-server`

## 6. Secure the server

- [ ] DON’T LOGOUT! Test that you can SSH from another terminal after every change.
- [ ] Change port number :
  - [ ] `sudo vim /etc/ssh/sshd_config`
  - [ ] `sudo vim /etc/systemd/system/sockets.target.wants/ssh.socket`
  - [ ] `sudo systemctl daemon-reload`
  - [ ] `sudo systemctl restart ssh`
- [ ] Allow SSH logins only for specific users
  - [ ] `sudo vim /etc/ssh/sshd_config`
  - [ ] `AllowUsers user1 user2`
  - [ ] `sudo systemctl restart ssh`
  - [ ] Test with root user (should not be able to SSH anymore)
- [ ] Don't permit Root login via SSH:
  - [ ] `sudo vim /etc/ssh/sshd_config`
  - [ ] `PermitRootLogin yes` to `PermitRootLogin no`
- [ ] Password Authentication
  - [ ] Ensure that `.ssh/authorized_keys` exist in user ash
  - [ ] `sudo vim /etc/ssh/sshd_config`
  - [ ] `PasswordAuthentication yes` to `PasswordAuthentication no`
  - [ ] `sudo vim /etc/ssh/sshd_config.d/*.conf`
  - [ ] `PasswordAuthentication yes` to `PasswordAuthentication no`
  - [ ] `sudo systemctl restart ssh`
  - [ ] Test connection
  - [ ] REMEMBER: To add `.ssh/authorized_keys` to used ash’s .ssh folder!!!!!
- [ ] Install and configure [fail2ban](../secondary/setup_fail2ban.md)
- [ ] Setup a firewall using [Uncomplicated Firewall (UFW)](../secondary/setup_ufw.md)
- [ ] Configure `/etc/hosts.allow` & `/etc/hosts.deny`: with home, office, and DO IPs (Especially if it is a database server)
  - [ ] `sudo vim /etc/hosts.allow ` and then `ALL: 192.168.1.50`
  - [ ] `sudo vim /etc/hosts.deny` and then `ALL: ALL`

## Setup backup scripts on VPS (by project)

- [ ] [Guide here](../secondary/backup_vps_to_s3.md)

## Setup Github Action for Auto Deplyment

- [ ] [Guide here](../secondary/github_actions_auto_deployment.md)

## Check logs on security

- [ ] `sudo last`: provides a list of all users and terminals that have logged into the server. reads data from the /var/log/wtmp file.
- [ ] `sudo lastb`: The lastb command shows the bad login attempts.
- [ ] `grep 'Failed password' /var/log/auth.log` : All authentication related events, successful or not, are logged in /var/log/auth.log file
- [ ] `grep 'Accepted password' /var/log/auth.log`: All authentication related events, successful or not, are logged in /var/log/auth.log file
- [ ] `lastlog` : displays the details of the last login of all users or of a given user. It's important to note that this command doesn't show any IP addresses
- [ ] `sudo cat /var/log/fail2ban.log` : Fail2Ban logs its actions here
