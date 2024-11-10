# How to backup the contents of your VPS

- [ ] Configure DO spaces bucket: Get access & secret keys
- [ ] Install s3cmd in the VPS
- [ ] Test s3cmd
- [ ] Create a folder for your VPS backups

**1. Install s3cmd**
- [ ] `sudo apt-get install s3cmd`
- [ ] `s3cmd --version`
- [ ] `s3cmd --configure`
  - [ ] Enter Access & Secret Key
  - [ ] For S3 Endpoint: `sgp1.digitaloceanspaces.com`
  - [ ] DNS-style bucket+hostname:port template: %(bucket)s.sgp1.digitaloceanspaces.com
  - [ ] Encryption password: [Leave empty]
  - [ ] Path to GPG program: [Leave empty]
  - [ ] Use HTTPS protocol [Yes]: Yes
  - [ ] HTTP Proxy server name: [Leave empty]
- [ ] At the end, you should receive: `Configuration saved to '/home/ash/.s3cfg'`

**2. Test s3cmd**

- [ ] List all spaces (buckets): `s3cmd ls`
- [ ] Create a simple test file: `echo "Hello Digital Ocean Spaces" > test.txt`
- [ ] Upload the test file: `s3cmd put test.txt s3://backups-production/test.txt`
- [ ] List contents of your space: `s3cmd ls s3://backups-production/`
- [ ] Download the file with a different name to verify: `s3cmd get s3://backups-production/test.txt downloaded-test.txt`
- [ ] View the downloaded file: `cat downloaded-test.txt`
- [ ] Clean up: 
  - [ ] `s3cmd del s3://backups-production/test.txt`
  - [ ] `rm test.txt downloaded-test.txt`

**3. Create a folder for your VPS backups**

**CAUTION**: DO NOT use the main folder //backups-production/ to save your backups. Each project needs to have its own folder.

- [ ] Create an empty file: `touch dummy.txt`
- [ ] Upload it to create the folder structure: `s3cmd put dummy.txt s3://backups-production/projectXYZ/dummy.txt`
- [ ] Verify the folder was created by listing its contents: `s3cmd ls s3://backups-production/projectXYZ/`
- [ ] Remove the dummy file: `s3cmd del s3://backups-production/projectXYZ/dummy.txt`
- [ ] Remove the dummy file: `rm dummy.txt`
- [ ] Now you have a folder called `projectXYZ` in your `backups-production` bucket

**4. Create backup.sh script**

- [ ] `touch backup.sh`
- [ ] `chmod +x ~/backup.sh`
- [ ] Save the script at the end of this file to `backup.sh`
-[ ] Run script: `./backup.sh`

**5. Schedule this on a cron job**

- [ ] `crontab -e`
- [ ] `0 */12 * * * /home/ash/backup.sh >> /home/ash/backup.log 2>&1`
- [ ] `crontab -l`
- [ ] check if the cron service is running: `systemctl status cron`

**6. How to restore backup**

- [ ] Get the backup: `s3cmd get s3://backups-production/xyz.tar.gz`
- [ ] To extract everything: `tar -xzf xyz.tar.gz`
- [ ] To just view the contents without extracting: `tar -tzf xyz.tar.gz`
- [ ] If you want to extract to a specific directory:
  - [ ] `mkdir backup-contents`
  - [ ] `tar -xzf xyz.tar.gz -C backup-contents`


**`backup.sh`**

```
#!/bin/bash

# Exit on error, undefined variables, and propagate ERR trap to functions
set -euo pipefail

# Configuration
BACKUP_DIR="/tmp/backup_$(date +%Y%m%d_%H%M%S)"
SPACE_BUCKET="s3://backups-production/projectXYZ"
BACKUP_NAME="projectXYZ-backup-$(date +%Y%m%d-%H%M%S)"
LOG_FILE="/home/ash/backup.log"

# Define items to backup (add or remove paths as needed)
BACKUP_ITEMS=(
    "/home/ash/db/db.sqlite"
    "/home/ash/config.json"
    "/home/ash/prompts"
    "/home/ash/web"
)

# Logging function
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Error handler
error_handler() {
    log "Error occurred in backup script (Line $1)"
    rm -rf "$BACKUP_DIR" 2>/dev/null || true
    exit 1
}

# Set error handler
trap 'error_handler $LINENO' ERR

# Start backup process
log "Starting backup process..."

# Create temporary backup directory
mkdir -p "$BACKUP_DIR"
log "Created temporary directory: $BACKUP_DIR"

# Backup each item
for item in "${BACKUP_ITEMS[@]}"; do
    if [ -e "$item" ]; then
        log "Processing: $item"

        # If it's the SQLite database
        if [[ "$item" == *".sqlite" ]]; then
            log "Creating SQLite backup..."
            sqlite3 "$item" ".backup '$BACKUP_DIR/database.sqlite'"

        # For directories
        elif [ -d "$item" ]; then
            log "Copying directory: $item"
            cp -r "$item" "$BACKUP_DIR/"

        # For regular files
        else
            log "Copying file: $item"
            cp "$item" "$BACKUP_DIR/"
        fi
    else
        log "Warning: Item not found: $item"
    fi
done

# Create tarball of all backed up items
log "Creating tarball..."
cd "$BACKUP_DIR"
tar -czf "${BACKUP_DIR}.tar.gz" ./*

# Upload to Digital Ocean Spaces
log "Uploading to Digital Ocean Spaces..."
s3cmd put "${BACKUP_DIR}.tar.gz" "$SPACE_BUCKET/${BACKUP_NAME}.tar.gz"

# Clean up
log "Cleaning up temporary files..."
rm -rf "$BACKUP_DIR"
rm -f "${BACKUP_DIR}.tar.gz"

log "Backup completed successfully"

# List recent backups
log "Recent backups in Digital Ocean Spaces:"
s3cmd ls "$SPACE_BUCKET/" | tail -n 5
```
