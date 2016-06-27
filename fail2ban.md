Fail2Ban
========

Setup
-------

### Install
- Good Notes: https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-centos-6
- Install wit habove tutorial and no defaults need to be changed
- If you wan to get emails when Fail2Ban does stuff (starts, stops, bans ips, etc.)
  - change the `dest` email in the `[ssh-iptables]` jail to your email (in `/etc/fail2ban/jail.local`)

```
[ssh-iptables]

enabled  = true
filter   = sshd
action   = iptables[name=SSH, port=ssh, protocol=tcp]
           sendmail-whois[name=SSH, dest=youremail@domain.com, sender=fail2ban@example.com]
logpath  = /var/log/secure
maxretry = 5
```

### Start on Boot/ReBoot
- update `/etc/rc.local` to start fail2ban
  - add this line: `service fail2ban start`

### Permanently Ban Repeat Offenders
- http://stuffphilwrites.com/2013/03/permanently-ban-repeat-offenders-fail2ban/

UnBan IP
--------
- `fail2ban-client set JAIL_NAME unbanip 1.2.3.4`
- default to unban an SSH/SFTP IP: `fail2ban-client set ssh-iptables unbanip 1.2.3.4`
- http://serverfault.com/questions/285256/how-to-unban-an-ip-properly-with-fail2ban
