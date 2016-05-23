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
