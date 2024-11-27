# Managing Databases

Steps

1. Use Logical Volume Manager (LVM) to create a new volume group
2. Change where the data is stored in mariadb-server
3. Backup and restore databases

## Change the location of the database storage for mariadb-server

Use this in case you want to change the location of the default mariadb-server installation to a different location, say a mounted volume.

- `grep 'datadir' /etc/mysql/mariadb.conf.d/50-server.cnf`: check the current location of the database
- `mysql -u root -p -e "SHOW VARIABLES LIKE 'datadir';`: check the current location of the database
- `sudo systemctl stop mariadb`: stop the mariadb service
- `sudo rsync -av /var/lib/mysql /mnt/db-secondary`: copy the database to the new location
- `sudo mv /var/lib/mysql /var/lib/mysql.bak`: move the old database to a backup location
- `sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf`: Edit the MariaDB configuration file
- `datadir         = /mnt/db-secondary/mysql`: Look for the datadir line and change its path to the new one.
- `sudo chown -R mysql:mysql /mnt/db-secondary/mysql`: change ownership of the new database location
- `sudo restorecon -Rv /mnt/db-secondary/mysql`: restore the security context of the new database location (Ensure selinux context is the same (if selinux is enabled in your OS))
- `sudo systemctl start mariadb`: restart the mariadb service
- `sudo systemctl status mariadb`: check the status of the mariadb service
- `mysql -u root -p -e "SHOW VARIABLES LIKE 'datadir';`: check the current location of the database
- You should see:

```
+---------------+-----------------------+
| Variable_name | Value                 |
+---------------+-----------------------+
| datadir       | /mnt/db-secondary/mysql/ |
+---------------+-----------------------+
```

## Backup and Restore databases

### Backup a database

- `mysqldump -u admin -p --databases mysampledb > mysampledb.sql`: backup database
- NOTE: The name of the sql dump 'mysampledb' does not matter. What matters are the contents of the file. So when you run a restore, the original database with the original table names are re-created.
-

### Restore a backed-up database

- `sudo mariadb -u admin -p < mysampledb.sql`: restore database

### Sync the backed up database to a remote standalone server

- `chown username /mnt/db-static-backup`: change ownership of the backup directory to the user (so that it can be written in the next step)
- `rsync -avz -e 'ssh -p <portnumber>>' --progress --partial mysampledb.sql username@111.111.11.11:/mnt/db-static-backup/`

## Setting up a secondary database server

- You need 2 servers: the primary and the secondary
- The primary server is the one that is currently running the database
- The secondary server is the one that will be used to backup the database

**STEP 1**

On the primary server

- `sudo nano /etc/mysql/conf.d/mysql.cnf`:
- Add additional lines as below under `[mysqld]`:

```
[mysqld]
log-bin
binlog-do-db=mysampledb
server-id=1
```

- `log-bin`: enables binary logging
- `binlog-do-db=mysampledb`: tells the server to log only the mysampledb database
- `server-id=1`: sets the server id to 1

**STEP 2**

On the primary server:

- `sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf`
- In this file, we have the following line: `bind-address = 127.0.0.1`
- With this default setting, the mysql daemon is only listening for connections on localhost (127.0.0.1), which is a problem since we’ll need to connect to it from another machine (the secondary server).
- Change this line to the following: `bind-address = 0.0.0.0`

**STEP 3**

- Access the MariaDB shell on the primary server and execute the following command: `GRANT REPLICATION SLAVE ON *.* to 'replicate'@'192.168.1.204' identified by 'password';`
- Create a replication user on the primary server.
- Allow it to connect to our primary server from the secondary server.
- IP should be that of **secondary server**.

**STEP 4: restart the MariaDB service**

On the primary server:

- `sudo systemctl restart mariadb`: restart the MariaDB service

**STEP 5: Lock the primary database and prevent additional changes**

On the primary server:

- `FLUSH TABLES WITH READ LOCK;`: lock the database

**STEP 6: Synchronize the primary and secondary server before we start**

On the primary server

- `mysqldump -u admin -p --databases mysampledb > mysampledb.sql`: backup database

On the primary server: Transfer the backup file to the secondary server

- `rsync -avz -e 'ssh -p 2122' --progress --partial mysampledb.sql username@111.111.11.11:/mnt/db-static-backup/`
- `rsync -avz -e 'ssh -i /path_to_private_key -p 2122' --progress --partial mysampledb.sql username@111.111.11.11:/mnt/db-static-backup/`

On the secondary server

- `mariadb -u admin -p < /mnt/db-static-backup/mysampledb.sql`: restore database

**STEP 7: Setup server ID on secondary database**

On the secondary server:

- `sudo nano /etc/mysql/conf.d/mysql.cnf`
- Add additional lines as below under `[mysqld]`:

```
[mysqld]
server-id=2
```

**STEP 8: Restart the MariaDB service on the secondary server**

On the secondary server:

- `sudo systemctl restart mariadb`: restart the MariaDB service

**STEP 9: Setup replication on the secondary server**

On the secondary server:

- `mariadb -u root -p`: access the MariaDB shell
- ` CHANGE MASTER TO MASTER_HOST="192.168.1.184", MASTER_USER='replicate', MASTER_PASSWORD='password';`

**STEP 10: Unlock the primary server tables**

On the primary server:

- `UNLOCK TABLES;`: unlock the primary server tables

**STEP 11: Check the status of the secondary server**

On the secondary server:

- `SHOW SLAVE STATUS\G`: check the status of the secondary server
- we should see the following line in the output: `Slave_IO_State: Waiting for master to send event`
- If the secondary server isn’t running (Slave_IO_State is blank), execute the following command: `START SLAVE;`
- `SHOW SLAVE STATUS\G`: check the status of the secondary server

**From this point forward, any data you add to your database on the primary server should be replicated to the secondary. YAY!!**

**STEP 12: Test the replication**

Insert data in the primary server and check if it is replicated in the secondary server.
