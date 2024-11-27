# Setup a firewall using Uncomplicated Firewall (UFW)

- [ ] `sudo apt install ufw`
- [ ] `sudo ufw status`
- [ ] `sudo ufw allow ssh`: allow port 22 for ssh
- [ ] If server serves web
  - [ ] `sudo ufw allow 80`
  - [ ] `sudo ufw allow 443`
- [ ] `sudo ufw default allow outgoing`
- [ ] `sudo ufw default deny incoming`
- [ ] `sudo ufw allow from 192.168.10.124 to any port 2122`: allow specific IP to access specific port
- [ ] `sudo ufw logging on`
- [ ] `sudo ufw enable`
