# Connect to networks via SSH

1. Set the hostname
2. Manage the network interface
3. Assign a static IP address
4. OpenSSH & key management
   1. Install OpenSSH
   2. Ensure that the OpenSSH service is running
   3. Generate public and private keys
      1. Goto: `/home/user/.ssh` folder
      2. `ssh-keygen` or `ssh-keygen -t rsa -b 4096` or `ssh-keygen -t rsa -b 4096 -C "my_comment"`
      3. `ssh-keygen -t ed25519` or `ssh-keygen -t ed25519 -C ""`
   4. Copying public keys to remote servers: `ssh-copy-id -p 2222 -i ~/.ssh/id_rsa.pub username@192.168.1.150`
   5. Utilizing an SSH agent:
      1. Start the agent on the local machine: `eval $(ssh-agent)`
      2. Give the path to the private key: `ssh-add ~/.ssh/id_rsa`
      3. Save catchphrase when asked for it.
      4. Connect to the remote server. No more passphrase required.
   6. Change the passphrase of an OpenSSH key
      1. `ssh-keygen -p`
      2. Then enter the directory path to the private key
      3. Enter new catchphrase
   7. Simplify SSH connections with a config file
      1. Go to local machine config file: `nano /home/user/.ssh/config`
      2. Edit file with Hostname, Host, Port, User, ...
   8. Disable password based authentication
