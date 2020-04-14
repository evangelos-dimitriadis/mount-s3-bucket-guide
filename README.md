# mount-s3-bucket-guide
A small guide of how to mount s3 buckets and create back-up repositories into them.
If you want to avoid mounting the s3 bucket locally you can use restic. The main drawback of restic
is that it doesn't support compression.

## Mount an AWS s3 bucket

There are multiple proggramms for that can mount a bucket, such as s3fs, rclone.
In the examples the backup folder's name is 'try_borg' and the s3 bucket 'borgbackup-demo'.

### s3fs

1. Install s3fs.
2. Store your credentials in a file containing either <access_key>:<secret_key> in file /etc/passwd-s3fs
3. Mount the folder `try_borg`:

`s3fs -o url=https://s3.us-east-1.amazonaws.com -o use_path_request_style borgbackup-demo try_borg -o passwd_file=~/.passwd-s3fs`

### Rclone

1. Install Rclone
2. rclone config  
3. rclone mount aws-s3-bucket:borgbackup-demo try_borg

## Create a backup to an s3 bucket

1. Install borg.
2. Init borg repo. 
`borg init --encryption=none dameko@localhost:/home/dameko/Documents/try_borg/`
3. I got a lot of errors while trying to initialize and backup to the folder while mounted with the default permissions. Thus, I chose to not use the root user at all. In production you may need to create a new user, disable root access etc.
```
sudo chmod 700 -R /home/dameko/.config/borg
sudo chmod 700 -R /home/dameko/.cache/borg

sudo chown -R dameko:dameko /home/dameko/.cache/borg/
sudo chown -R dameko:dameko /home/dameko/.cache/borg
```
3. Create backup. First is just a tag. You could something with a date or a timestamp
`borg create try_borg/::First monetdb-backup/s3temp.tar`
4. Restore backup.
`borg extract try_borg/::First`
