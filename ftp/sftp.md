SFTP using OpenSSH
==================

References
----------
- unix.stackexchange.com: http://unix.stackexchange.com/questions/110593/how-can-i-create-an-sftp-user-in-centos
- how to setup sftp on centos (not super helpful): https://wiki.centos.org/HowTos/sftp
- how to setup chrooted sftp (really good): http://www.thegeekstuff.com/2012/03/chroot-sftp-setup/
  - prevents users from navicagting rooted file system
- article on limiitng sftp access: http://www.cyberciti.biz/tips/howto-linux-shell-restricting-access.html
- article on folder permissions in linux: http://unix.stackexchange.com/questions/18095/in-linux-write-permission-is-equivalent-to-execute-for-directories
  - another on the parent folders: http://unix.stackexchange.com/questions/13858/do-the-parent-directorys-permissions-matter-when-accessing-a-subdirectory
- sftp chroot tut: https://wiki.archlinux.org/index.php/SFTP_chroot

Setup
-----

### setup basic sftp
- install open ssh: `sudo yum install openssh`
- allow ssh (sftp) login with passwords and not just key files
  - `sudo vim /etc/ssh/sshd_config`
  - set `PasswordAuthentication no` to `PasswordAuthentication yes` 
- restart sshd: `sudo service sshd restart`
- do not add users until the final step below

### limit user access (chrooted sftp)
- add a group: `sudo groupadd sftpusers`
- create sftp home folder
  - `sudo mkdir /sftp`
  - `sudo chown root:root /sftp`
  - `sudo chmod 755 /sftp`
- add user group settings to the `/etc/ssh/sshd_config`

```
  # tail /etc/ssh/sshd_config
  Match Group sftpusers
        ChrootDirectory /sftp/%u
        ForceCommand internal-sftp
        AllowTcpForwarding no
        PermitTunnel no
        X11Forwarding no
```

### Add a User
- add user:
  - `sudo useradd -g sftpusers -d / -s /sbin/nologin userName`
    - `-g sftpusers` is the group the user is part of
    - `-d /` is the root directory (relative to the user's root directory)
    `-s /sbin/nologin` prevents root access to filesystem
- set password: `sudo passwd userName`
- create user home folder
  - `sudo mkdir -m 755 /sftp/userName`
  - usually already done by default: `sudo chown root:root /sftp/userName`
- create user upload and download folders
  - `sudo mkdir -m 755 /sftp/userName/upload`
  - `sudo mkdir -m 755 /sftp/userName/download`
  - `sudo chown userName:root /sftp/userName/upload`
  - usually already done by default: `sudo chown root:root /sftp/userName/download`
- restart sshd
  - don't need to do when just adding a new user and their folders  
  - `sudo service sshd restart`

### Whitelist IPs
- add incoming SSH port 22 IP addresses for users so in the AWS console for whatever security group you are using for this server.

### Logging
- only SSH/SFTP connection details are tracked by default at 
- log files are here by default: `/var/log/secure`
- you can enable logging of SFTP commands by doing this
  - add `-l INFO` to this line of `/etc/sshd/sshd_config` - `Subsystem sftp  /usr/libexec/openssh/sftp-server`
  - eg. `Subsystem sftp  /usr/libexec/openssh/sftp-server -l INFO`
  - restart sshd
- Notes: https://en.wikibooks.org/wiki/OpenSSH/Logging

### Security
- test server with [nmap](nmap.md)

S3 backed SFTP
--------------
- see the other doc on [S3FS](./aws/s3fs.md)
