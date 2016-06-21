S3FS Backed SFTP Server
=======================

Install S3FS
------------
- [my notes](/aws/s3fs.md)
- don't mount any folders until later in this process
- each SFTP user will have their own S3 home folder mounted inside their local home folder on the SFTP server

Enable SFTP with OpenSSH
------------------------
- allow ssh (sftp) login with passwords and not just key files
  - sudo vim /etc/ssh/sshd_config
  - set PasswordAuthentication no to PasswordAuthentication yes
  - add configurations to secure SFTP to end of sshd_config

```
Match Group sftpusers
  ChrootDirectory /sftp/%u
  ForceCommand internal-sftp
  AllowTcpForwarding no
  PermitTunnel no
  X11Forwarding no
```

- restart sshd: `sudo service sshd restart`

Create Group
------------
- `sudo groupadd ftpusers`
  - any group name is fine

Create User
-----------
- `sudo useradd -g ftpusers -s /sbin/nologin userName`
  - home directory will default to `/home/userName`
- create the user's home directory
  - `sudo mkdir /home/userName`
  - default permissions and ownership are good (`drwxr-xr-x 2 root root`)
- create a folder called `files` inside the user's home directory
  - `sudo mkdir /home/userName/files/`
  - this is where we'll mount their bucket "folder"
  - permissions and ownership don't matter as they will be overwritten when we mount a bucket here
- mount the user's S3 home folder to the `files` folder inside their local `/home/` folder
  - mount `s3://bucket/userName/` to `/home/userName/files/`
  - `sudo s3fs nwd-ftp:/userName/ /home/userName/files/ -o iam_role=ftp-server -o endpoint=us-west-2 -o allow_other -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5 -o umask=022 -o uid=501`
  - `uid` must be the user's linux user id (use this: `cat /etc/passwd`)

Mount on Boot/ReBoot
--------------------
This can be done using rc.local or fstab (or various other methods)
- rc.local
  - add each user's S3FS mount command to `/etc/rc.local` (see example mount command above)
  - run rc.local with `/etc/rc.local`
- fstab  
  - add each user's S3FS mount command to `/etc/ftab`
  - mount new drives using `mount -a` which will read fstab and mount any new devices
