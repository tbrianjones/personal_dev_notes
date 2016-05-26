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
  - config notes for proftpd on ec2: http://www.ajohnstone.com/achives/setting-up-an-ftp-server-with-proftpd-on-ec2-2/
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
# This is a basic ProFTPD configuration file (rename it to 
# 'proftpd.conf' for actual use.  It establishes a single server
# and a single anonymous login.  It assumes that you have a user/group
# "nobody" and "ftp" for normal operation and anon.

# NetWise Conf
# - lots of conf ideas were taken from here
# - http://www.techrepublic.com/article/lock-it-down-set-up-a-secure-ftp-server-with-proftpd/

ServerName                      "NetWise Data FTP"
ServerType                      standalone
DefaultServer                   on

# Port 21 is the standard FTP port.
Port                            21

# enable ssl (ftps)
<IfModule mod_tls.c>
  TLSEngine                  on
  TLSLog                     /var/log/proftpd/tls.log
  TLSProtocol TLSv1.2
  TLSCipherSuite AES128+EECDH:AES128+EDH
  TLSOptions                 NoCertRequest AllowClientRenegotiations
  TLSRSACertificateFile      /etc/proftpd/ssl/proftpd.cert.pem
  TLSRSACertificateKeyFile   /etc/proftpd/ssl/proftpd.key.pem
  TLSVerifyClient            off
  TLSRequired                on
  RequireValidShell          no
</IfModule>

MasqueradeAddress               54.197.9.10 # set a static ip for server
PassivePorts                    60000 65535

<IfModule mod_facts.c>
  FactsAdvertise off
</IfModule>

# Don't use IPv6 support by default.
UseIPv6                         off

# Umask 022 is a good standard umask to prevent new dirs and files
# from being group and world writable.
Umask                           022

# To prevent DoS attacks, set the maximum number of child processes
# to 30.  If you need to allow more than 30 concurrent connections
# at once, simply increase this value.  Note that this ONLY works
# in standalone mode, in inetd mode you should use an inetd server
# that allows you to limit maximum number of processes per service
# (such as xinetd).
MaxInstances                    30

# Set the user and group under which the server will run.
User                            proftpd
Group                           proftpd

# To cause every FTP user to be "jailed" (chrooted) into their home
# directory, uncomment this line.
DefaultRoot ~/

# fake directory info
DirFakeUser                     on      user
DirFakeGroup                    on      group
DirFakeMode                     0640

# Normally, we want files to be overwriteable.
AllowOverwrite                  off

# Disallow clients from any access to hidden files.
<Limit READ DIRS>
  IgnoreHidden                  on
</Limit>

# Bar use of SITE CHMOD by default
<Limit SITE_CHMOD>
  DenyAll
</Limit>

# logging
TransferLog /var/log/proftpd/xferlog.default
LogFormat default "%h %l %u %t \"%r\" %s %b"
LogFormat auth "%v [%P] %h %t \"%r\" %s"
LogFormat write "%h %l %u %t \"%r\" %s %b"
UseReverseDNS on

# general config
<Global>
  Umask 022
  DeferWelcome on
  IdentLookups on
  ExtendedLog /var/log/proftpd/access.log WRITE,READ write
  ExtendedLog /var/log/proftpd/auth.log AUTH auth
  ExtendedLog /var/log/proftpd/paranoid.log ALL default
</Global>

# turn off passive connections to prevent the first user command from hanging
# - not entirely sure what this does, but didn't want to open up firewall ports to deal with this
# - http://www.proftpd.org/docs/howto/NAT.html
#<Limit EPSV PASV>
#  DenyAll
#</Limit>
```

### test it
- https://ftptest.net/

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
- setup ssl: http://notes.sagredo.eu/node/3
