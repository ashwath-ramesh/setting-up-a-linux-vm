# Rapid Fire Checklist

Create a new droplet/server node
- [ ] Create a new droplet on digital ocean / linode
- [ ] Don't use SSH for root login. Setup only password. (Since later in settings, no root login /SSH is allowed)

Login as root
- [ ] ```sudo adduser ash``` Create a new user
- [ ] ```sudo usermod -aG sudo ash``` Add ash to sudo group 
- [ ] ```getent group sudo``` Check ash is in list of sudo-ers
- 