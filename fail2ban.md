Fail2Ban
========

Install
-------
- default setup below is pretty easy:
- https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-centos-6

UnBan IPS
---------
- `fail2ban-client set JAIL_NAME unbanip 1.2.3.4`
- default to unban an SSH/SFTP IP: `fail2ban-client set ssh-iptables unbanip 1.2.3.4`
- http://serverfault.com/questions/285256/how-to-unban-an-ip-properly-with-fail2ban
