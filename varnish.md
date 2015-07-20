Varnish
=======


Common Varnish Functions
------------------------

### Removing a specific file from the Cache

*banning an object from cache*

- use the varnishadm utility
  - launch varnishadm by typing `varnishadm` at the cli
  - use the ban.url function and use a regular expression to specify the url to ban
    - `ban.url ^/companies/profile/33310/industrologic-inc`
    - removes that company profile page ( obsoletes it ) from the active varnish cache

### using the varnishadm utility
- type `varnishadm` at the command line
- type `help` inside the admin utility to see a list of commands
- type the name of the command to learn more about it


Log HTTP Requests with Varnish
------------------------------

### Enabling HTTP Logging with Varnish
- ON industrycortex.com
  - add this line to `rc.local`: `varnishncsa -a -w /data/varnish/log/access.log -D -P /var/run/varnishncsa.pid`
  - then execute the same line from the CLI
- Good, Simple Tutorial ( not entirely accurate )
  - http://www.garron.me/en/go2linux/configure-varnish-logs-varnishnsca-logrotate-and-awstats.html
- Run varnishncsa to produce HTTP logs
  - `varnishncsa -a -w /var/log/varnish/access.log -D -P /var/run/varnishncsa.pid`
    - a: To append the logs to an already existing file
    - w: To write the logs to the /var/log/varnish/access.log file
    - D: To run varnishncsa as a daemon
    - P: To write the PID file in the /var/run/ folder
  - This command will put the pid and logs in the default location
    - we change this on the cortex, to put the logs on a bigger drive ( `/data/varnish/log/access.log` )
  - Also add this command to `/etc/rc.local` so varnish starts logging on a reboot.

### Apache
Apache ( or other backend webserver ), is likely only seeing Varnish Cache misses, so it's logs will not be accurate.  The backend webserver probably doesn't even need to be keeping these logs, so they can be turned off ( unless you want to compare cache hits to misses, but there's better varnish logging to do this ).


Install and Setup Varnish
-------------------------

*web caching - reverse proxy in front of apache*

- `sudo yum install varnish*`
- copy /etc/varnish/default.vcl to cortex_varnish_config.vcl
- edit cortex_varnish_config.vcl
  - change default backend port to 8080 where apache will be listening ( .port = "8080"; )
- edit /etc/sysconfig/varnish
  - change port varnish listens on to 80 ( VARNISH_LISTEN_PORT=80 )
  - change default config file to new one ( VARNISH_VCL_CONF=/etc/varnish/cortex_varnish_config.vcl )
- edit /etc/httpd/conf/httpd.conf
  - change listen to port 8080 ( Listen 8080 )
- create symlinks
  - create a varnish directory on /data to store the varnish cache file ( gets as large as you set the cache to )
    - create /data/varnish/
    - delete /var/lib/varnish/
    - create a symlink from /var/lib/varnish/ to /data/varnish ( sudo ln -s /data/varnish /var/lib/varnish )
  - symlink to varnish config directory, containing varnish vcl config file
    - delete /etc/varnish and all files in it
    - create symlink, sudo ln -s /data/www/directory/configs/varnish/ /etc/varnish/
  - symlink to main varnish config file
    - delete /etc/sysconfig/varnish ( this is a file, not a directory )
    - create symlink, sudo ln -s /data/www/directory/configs/varnish/varnish /etc/sysconfig/varnish
- restart ( or start ) varnish ( sudo /etc/init.d/varnish start )
- restart ( or start ) httpd
- it's working now, but it's not caching stuff ... prob a result of pages being different each time, or cookies or something.
  - monitor with $ varnishstat
  - monitor logs with $ varnishlog
  - see urls being requested ( $ varnishtop -i rxurl )
  - cli tool - $ varnishadm
