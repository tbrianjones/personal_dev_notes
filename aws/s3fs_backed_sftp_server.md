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
  ForceCommand internal-sftp -l INFO # -l INFO enables logging to /var/log/secure
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
- `sudo useradd -M -g ftpusers -s /sbin/nologin userName`
  - -M prevents the home directory from creating (it's ownership and permissions will all be wrong anyway)
- create home folder
  - `sudo mkdir /home/userName`
  - the default owner and permissions will be correct (root:root / 755)
- create a folder called `files` inside the user's home directory
  - `sudo mkdir /home/userName/files/`
  - this is where we'll mount the user's bucket "folder"
  - permissions and ownership don't matter on this folder as they will be overwritten when we mount a bucket here
- create a user password
  - `sudo passwd userName`
  - enter passwords as prompt

### Mount the User's Bucket-Folder
- create user folder in s3
  - `s3://sftpBucket/userName/`
- mount the user's S3 home folder to the `files` folder inside their local `/home/` folder
- ie. mount `s3://sftpBucket/userName/` to `/home/userName/files/`
  
- Use one of these methods (`uid`, below, must be the user's linux user id (use this: `cat /etc/passwd`))
  - Using S3FS Command Directly from the command line:
    - `sudo s3fs sftpBucket:/userName/ /home/userName/files/ -o iam_role=ftp-server -o endpoint=us-west-2 -o allow_other -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5 -o umask=022 -o uid=501`
  - add a device to `/etc/fstab`:
    - `sftpBucket:/userName/ /home/userName/files/ fuse.s3fs _netdev,iam_role=ftp-server,endpoint=us-west-2,allow_other,stat_cache_expire=10,enable_noobj_cache,enable_content_md5,umask=022,uid=501 0 0`
    - use `mount -a` to load the new device from fstab once it's added
    - This error is ok if the listed bucket is already mounted: `s3fs: MOUNTPOINT directory /home/intelldata/files is not empty`
  
Mount on Boot/ReBoot
--------------------
This can be done using rc.local or fstab (or various other methods)
- rc.local
  - add each user's S3FS mount command to `/etc/rc.local` (see example mount command above)
  - run rc.local with `/etc/rc.local`
- fstab  
  - add each user's S3FS mount command to `/etc/ftab`
  - mount new drives using `mount -a` which will read fstab and mount any new devices

Automating This Process
-----------------------
- this has not been attempted yet
- setup cron to read all user folders in `S3://sftpBucket/`
- when a new user is found, execute a shell script that:
  - gens the new user
  - creates a random password
  - emails the password to the admin
  - adds the S3FS mount command to the desired location
  - mounts the new S3 user folder to their home folder
