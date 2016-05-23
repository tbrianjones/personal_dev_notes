ProFTPd
=======

Installation
------------
- download the most recdent release from github: https://github.com/proftpd/proftpd/releases
- unzip it, cd into the folder, and use these commands to compile it:

```
./configure \
        --prefix=/usr/local \
        --without-pam --disable-auth-pam \
        --enable-openssl \
        --enable-ctrls \
        --with-modules=mod_ratio:mod_readme:mod_sftp:mod_tls:mod_ban:mod_ctrls_admin

make
make install
```

### Create a user:group for ProFTPd
- run this to create the user/group
  -  `useradd -r proftpd`
- edit `/etc/passwd`
  - make sure user `proftpd` has no home directory and no login
  - example: `proftpd:x:498:498:Proftpd:/:/sbin/nologin`
  - this should match the `nouser` user

### Create a log folder
- `/var/log/proftpd`
- `chmod proftpd:proftpd /var/log/proftpd`

### proftpd.conf
- used some notes from below
  - general config notes: http://www.techrepublic.com/article/lock-it-down-set-up-a-secure-ftp-server-with-proftpd/
  - sftp notes: http://xydmz.net/index.php/unix/sftp-only-server-with-proftpd-on-rhel5/

### create ssl certs to allow for ftps
- `sudo mkdir /etc/proftpd/ssl`
- `openssl req -new -x509 -days 365 -nodes -out /etc/proftpd/ssl/proftpd.cert.pem -keyout /etc/proftpd/ssl/proftpd.key.pem`
  - answer questions: most should be self explanatory
  - when asked for Common Name, enter your domain name: eg. ftp.domain.com
- `sudo chmod 600 /etc/proftpd/ssl/proftpd.*`

**sample conf**
```
```

Operation
---------
- change configurations
  - edit `/usr/local/etc/proftpd.conf`
- start ProFTPd
  - `sudo /usr/local/sbin/proftpd`
  - this will load updates to the `/usr/local/etc/proftpd.conf`
- stop ProFTPd
  - get the pid of ProFTPd to stop it
  - `ps aux | grep proftpd`
  - kill it: `kill pid_of_proftpd`

Resources
---------
- secure setup of proftpd: http://www.techrepublic.com/article/lock-it-down-set-up-a-secure-ftp-server-with-proftpd/
- setup ssl: http://notes.sagredo.eu/node/3
