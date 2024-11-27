# Manage databases

**Pre-requisite:** Use Logical Volume Manager (LVM) for the partition that houses your database files.

1. Install MariaDB:
   1. `sudo apt install mariadb-server`
   2. Check server is enabled: `systemctl status mariadb`
   3. Setup security: `sudo mysql_secure_installation`
      1. Enter current password for root (enter for none): `<enter>`
      2. Switch to unix_socket authentication [Y/n] n
      3. Change the root password? [Y/n] Y
      4. Remove anonymous users? [Y/n] Y
      5. Disallow root login remotely? [Y/n] Y
      6. Remove test database and access to it? [Y/n] Y
      7. Reload privilege tables now? [Y/n] Y
   4. Start mariadb: No need to enter root pwd `sudo mariadb` (or) Enter root pwd `mariadb -u root -p`
2. Update the MariaDB configuration files
   1. Directory: `/etc/mysql`
   2. Config file that mariadb reads on startup: `cat /etc/mysql/mariadb.cnf`
   3. Set default values for mariadb when it starts: `cat /etc/mysql/debian-start`
   4. Setup primary & secondary server relationships: `cat /etc/mysql/conf.d/mysql.cnf`
3. Manage the MariaDB database
   1. Basic setup (Define what users are going to do; principal of least privileges):
      1. root -> Manages users (and everything else)
      2. admin -> Manages database (but not user setup)
      3. readonlyuser -> Can only read database
   2. Setup `admin` and `readonlyuser`:
      1. Log in as root: `sudo mariadb`
      2. Setup admin (can only log in to the DB after first logging in to the local server)
         1. Based on log in to the DB from location:
            1. From locahost (Most secure): `CREATE USER 'admin'@'localhost' IDENTIFIED BY 'password';`
            2. From anywhere (Least secure): `CREATE USER 'admin'@'%' IDENTIFIED BY 'password';`
            3. From IP address: `CREATE USER 'admin'@'192.168.1.%' IDENTIFIED BY 'password';`
         2. `FLUSH PRIVILEGES;`
      3. Grant full permissions to admin user:
         1. Grant full access to DB: `GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost';`
         2. `FLUSH PRIVILEGES;`
      4. Grant read-only permissions to `readonlyuser`:
         1. Access to all databases (not recommended): `GRANT SELECT ON *.* TO 'readonlyuser'@'localhost' IDENTIFIED BY 'password';`
         2. Access to specific databases (recommended): `GRANT SELECT ON mysampledb.* TO 'readonlyuser'@'localhost' IDENTIFIED BY 'password';`
         3. `FLUSH PRIVILEGES;`
      5. Other grants:
         1. All access to a specific DB: `GRANT ALL ON mysampledb.* TO 'appuser'@'localhost' IDENTIFIED BY 'password';`
         2. `CREATE`, `DELETE`, `SELECT`, `INSERT`, `DROP`
      6. Drop user: `DROP USER 'myuser'@'localhost';`
      7. Check grants: `SHOW GRANTS FOR 'readonlyuser'@'localhost';`
   3. Create a database
      1. Log in as admin: `mariadb -u admin -p;`
      2. `CREATE DATABASE mysampledb;`
      3. `SHOW DATABASES;`
      4. List users: `SELECT HOST, USER, PASSWORD FROM mysql.user;`
      5. Change to a database: `USE mysampledb;`
         1. `CREATE TABLE Employees (Name char(15), Age int(3), Occupation char(15));`
         2. `SHOW COLUMNS IN Employees;`
         3. `INSERT INTO Employees VALUES ('Joe Smith', '26', 'Ninja');`
         4. `SELECT * FROM Employees;`
         5. `DELETE FROM Employees WHERE Name = 'Joe Smith';`
         6. `DROP TABLE Employees;`
         7. `DROP DATABASE mysampledb;`
   4. Backup a database (from Linux terminal): `mysqldump -u admin -p --databases mysampledb > mysampledb.sql;` (or) `mysqldump -u admin -p --databases mysampledb > /path/to/output/mysampledb.sql;`
4. Setup a secondary database server: [managing_databases.md](../secondary/managing_databases.md)
