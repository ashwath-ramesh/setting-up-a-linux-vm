# Secure the server

1. Lower the attack surface
2. Respond to CVEs
3. Automatically installing patches with Canonical Livepatch service
4. Install security updates
5. Securing OpenSSH (All changes to be made in file: `sudo nano /etc/ssh/sshd_config`)
   1. Change the port number on which OpenSSH listens
      1. Change line `Port 22 to Port 2222`
      2. Restart: `sudo systemctl restart ssh`
      3. From Ubunto 22.10,
      4. `sudo nano /etc/ssh/sshd_config`
         1. Change line `Port 22 to Port 2222`
         2. `sudo nano /etc/systemd/system/sockets.target.wants/ssh.socket`
         3. `Change ListenStream to ListenStream=2222` or any port number
         4. `sudo systemctl daemon-reload`
         5. `sudo systemctl restart ssh`
   2. Change to Protocol 2 from Protocol 1 (only for older Linux versions)
      1. Change line `Protocol 1 to Protocol 2`
      2. Restart: `sudo systemctl restart ssh`
   3. Allow SSH logins only for specific users
      1. First create a new group: `sudo groupadd sshusers`
      2. Then make one or more users members of that group: `sudo usermod -aG sshusers <myuser>`
      3. Goto file: `sudo nano /etc/ssh/sshd_config`
      4. `AllowUsers user1 user2`
   4. Don't permit Root login via SSH
      1. Change: PermitRootLogin yes to PermitRootLogin no
      2. Restart: `sudo systemctl restart ssh`
   5. Don't allow password authentication
      1. Change: PasswordAuthentication yes to PasswordAuthentication no
      2. Restart: `sudo systemctl restart ssh`
      3. Sometimes the sshd/config file contains: `Include /etc/ssh/sshd_config.d/*.conf`
         1. Goto: `sudo nano /etc/ssh/sshd_config.d/*.conf`
         2. Change: PasswordAuthentication yes to PasswordAuthentication no
         3. Change: PubkeyAuthentication no to PubkeyAuthentication yes
         4. Restart: `sudo systemctl restart ssh`
6. Install and configure Fail2ban
   1. Install: `sudo apt install fail2ban`
   2. You shouldn’t use its default config file. The default file is /etc/ fail2ban/jail.conf. Since this file can be overwritten. Fail2ban also reads the /etc/fail2ban/jail.local file, if it exists. the presence of a jail. local file will supersede the jail.conf file.
   3. Make a copy of jail. conf and save it as jail.local: `sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
   4. `sudo nano /etc/fail2ban/jail.local`
   5. The first configuration item to change is located on or around line 92 and is commented out: `#ignoreip = 127.0.0.1/8 ::1` Uncomment this.
   6. Add IPs that you want to whitelist: `ignoreip = 127.0.0.1/8 ::1 ip1 ip2`
   7. `bantime = 10m`
   8. `maxretry = 5`
   9. Jails: Look for this option underneath [sshd]: `port = ssh`. Change this to your new port number if applicable.
   10. Restart Fail2ban and then check its status:
       1. `sudo systemctl restart fail2ban`
       2. `sudo systemctl status -l fail2ban`
       3. `sudo fail2ban-client status`
7. MariaDB best practices for secure database servers
   1. Configure `/etc/hosts.allow` & `/etc/hosts.deny`: with home, office, and DO IPs (Especially if it is a database server)
8. Setup a firewall (Uncomplicated Firewall (UFW))
   1. IMPORTANT:
      - **Always allow SSH before enabling UFW to avoid getting locked out.**
      - **Use ufw status to check which rules are active.**
      - **Be cautious when setting rules; always ensure you have another way of accessing the server if something goes wrong.**
   2. STEPS:
      1. Install UFW: `sudo apt install ufw`
      2. Set default policies: `sudo ufw default deny incoming` & `sudo ufw default allow outgoing`
      3. Allow SSH Access (To ensure you don't lock yourself out, allow SSH traffic): `sudo ufw allow 2222/tcp`
      4. Allow Other Services (Optional): `sudo ufw allow http` & `sudo ufw allow https`
      5. Enable UFW Logging (Optional): `sudo ufw logging on`
      6. Check UFW Status and Rules: `sudo ufw status verbose`
      7. Enable/Disable UFW: `sudo ufw enable` & `sudo ufw disable`
9. Encrypt and decrypt disks using LUKS
10. Locking down SUDO

Check logs on security here

- `sudo last`: provides a list of all users and terminals that have logged into the server. reads data from the /var/log/wtmp file.
- `sudo lastb`: The lastb command shows the bad login attempts.
- `grep 'Failed password' /var/log/auth.log` : All authentication related events, successful or not, are logged in /var/log/auth.log file
- `grep 'Accepted password' /var/log/auth.log`: All authentication related events, successful or not, are logged in /var/log/auth.log file
- `lastlog` : displays the details of the last login of all users or of a given user. It's important to note that this command doesn't show any IP addresses
- `sudo cat /var/log/fail2ban.log` : Fail2Ban logs its actions here
