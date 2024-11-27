# Manage users and permissions

1. Manage users
   1. Create a user account
      1. [ ] Create a user: `sudo adduser <username>`
      2. [ ] Confirm user has been created: `cat /etc/passwd` & `sudo cat /etc/shadow`
      3. [ ] Login as user (with user's password): `su - <username>` (or) Login as user (with your password): `sudo su - <username>`
      4. [ ] Ensure that the user directory exists in `/home/<username>`
      5. [ ] Exit from <username>: `exit`
   2. Remove a user
      1. [ ] Delete the user: `sudo userdel <username>` (Does **not** remove contents of users' home directory)
      2. [ ] Move contents of users home directory to archives: `sudo mv /home/<username> /store/file_archive`
   3. Distribute a default configuration file (/etc/skel)
      1. [ ] View contents of directory: `ls -al /etc/skel`
      2. [ ] Add content ....
2. Manage groups
   1. View all groups in server: `cat /etc/group`
   2. View groups associated with a user: `groups <username>`
   3. Create a new group: `sudo groupadd <groupname>`
   4. Delete a group: `sudo groupdel <groupname>`
   5. Add user to a group: `sudo gpasswd -a <username> <groupname>` (or) `sudo usermod -aG <groupname> <username>`
   6. Remove user from a group: `sudo gpasswd -d <username> <groupname>`
   7. Change a user's primary group: `sudo usermod -g <groupname> <username>`
   8. Change a user's primary directory:
      1. Move old home dir to new home dir: `sudo usermod -d <new_dir_path> <old_username>`
      2. Change the username: `sudo usermod -l <new_username> <old_username>`
3. Manage password and password policies
   1. Lock a user account
   2. Unlock a user account
   3. Check when users password was last changed: `sudo passwd -S <username>`
   4. Set password expiration information
   5. Set a password policy
4. Configure administrator access with sudo
   1. Add users to the sudo group using `sudo usermod -aG sudo <username>`
   2. Add sudo to users using `visudo` (edit: /etc/sudoers)
      1. Users is able to use sudo from which terminals: xxx
      2. User is able to impersonate which users: xxx
      3. User is able to impersonate which groups: xxx
      4. User is able to run which commands: xxx
5. Set permission on files and directories
   1. Change permissions (using octal notations): `chmod [options] <permissions> <file>` (Ref: `https://quickref.me/chmod`)
      1. on a file: `chmod 755 foo.txt`
      2. on a directory: `chmod -R 755 my_directory`
   2. Change ownership:
      1. To a new user only:
         1. of a file: `chown <to_user> <filename>`
         2. of a directory (and all its files & dirs): `chown -R <to_user> <dir>`
      2. To a new group only:
         1. of a file: `chgrp <to_group> <filename>`
         2. of a directory (and all its files & dirs): `chgrp -R <to_group> <dir>`
      3. To a new user & new group: `chown <to_user>:<to_group> <filename>`
