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
- [ ] Remove the dummy file: `s3cmd del s3://backups-production/projectXYZ/dummy.txt
- [ ] Remove the dummy file: `rm dummy.txt`
- [ ] Now you have a folder called `projectXYZ` in your `backups-production` bucket
