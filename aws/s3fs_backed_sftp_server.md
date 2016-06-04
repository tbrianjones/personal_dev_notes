S3FS Backed SFTP Server
=======================

Install S3FS
------------
- [my notes](/aws/s3fs.md)
- don't mount it yet
- each user must have 



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
- 
