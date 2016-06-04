S3FS Backed SFTP Server
=======================

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

Install S3FS
------------
