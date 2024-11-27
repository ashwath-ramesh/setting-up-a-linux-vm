# SSH Keys Management

#### Generating an SSH key

- ssh-keygen (or)
- ssh-keygen -t rsa -b 4096 -C "my_comment"

#### Logging into a remote server using a specific private key

- ssh -i ~/.ssh/id_rsa root@123.456.789.012

#### Logging from a remote server into GitHub using a specific private key

- Test connection with: ssh -T git@github.com -i ~/.ssh/my_private_key

#### Adding in new public SSH keys to the vm

- go to: nano ~/.ssh/authorized_keys
- add the public key in a new line

#### Create a config file

- `nano ~/.ssh/config`
- Add text below

```
  Host your_custom_hostname
    HostName github.com
    User git
    IdentityFile /path/to/your/private_key
```
