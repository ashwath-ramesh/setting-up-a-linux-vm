# Share and transfer files

1. Setup NFS share
2. Transfer files with `rsync`
   1. Copy contents of a directory to another directory: `rsync -avr <source_dir> <destination_dir>`
   2. Copy over SSH to another server. Variations:
      1. `rsync -avz -e 'ssh config_hostname' :<source_dir> <destination_dir>`
      2. `rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine`
3. Transfer files with `scp`
