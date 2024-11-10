How to backup the contents of your VPS

- [ ] Configure DO spaces bucket
- [ ] Install s3cmd in the VPS & test it out

Install s3cmd: 
- [ ] `sudo apt-get install s3cmd`
- [ ] `s3cmd --version`
- [ ] `s3cmd --configure`
- [ ] Enter Access & Secret Key
- [ ] For S3 Endpoint: `sgp1.digitaloceanspaces.com`
- [ ] DNS-style bucket+hostname:port template: %(bucket)s.sgp1.digitaloceanspaces.com
- [ ]Encryption password: [Leave empty]
- [ ]Path to GPG program: [Leave empty]
- [ ]Use HTTPS protocol [Yes]: Yes
- [ ]HTTP Proxy server name: [Leave empty]
- [ ] At the end, you should receive: `Configuration saved to '/home/ash/.s3cfg'`

Test s3cmd

- [ ] list all spaces (buckets): `s3cmd ls`
- [ ] Create a simple test file: `echo "Hello Digital Ocean Spaces" > test.txt`
- [ ] Upload the test file: `s3cmd put test.txt s3://backups-main/test.txt`
- [ ] List contents of your space: `s3cmd ls s3://backups-main/`
- [ ] Download the file with a different name to verify: `s3cmd get s3://backups-main/test.txt downloaded-test.txt`
- [ ] View the downloaded file: `cat downloaded-test.txt`
- [ ] Clean up: 
  - [ ] `s3cmd del s3://backups-main/test.txt`
  - [ ] `rm test.txt downloaded-test.txt`
