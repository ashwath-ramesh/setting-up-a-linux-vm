# Install and configure fail2ban

- [ ] Install and configure fail2ban
  - [ ] `sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
  - [ ] `sudo vim /etc/fail2ban/jail.local`
  - [ ] Set these values under [sshd]
    - [ ] `enabled = true`
    - [ ] `mode = aggressive`
  - [ ] Optional values to set (located around line 92 and is commented out)
    - [ ] Add IPs that you want to whitelist: `ignoreip = 127.0.0.1/8 ::1 ip1 ip2`
    - [ ] `bantime = 10m`
    - [ ] `maxretry = 5`
  - [ ] `sudo systemctl enable fail2ban`
  - [ ] `sudo systemctl restart fail2ban`
  - [ ] `sudo systemctl status -l fail2ban`
  - [ ] `sudo fail2ban-client status`
