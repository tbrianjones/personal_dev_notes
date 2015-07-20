supervisord
===========


operating supervisord
---------------------
we should not have to run supervisord as root ( meaning don't use sudo ).  if crawling fails, permissions are not set right and should be adjusted on the system

### Run as a Daemon
Run supervisord `/usr/bin/supervisord` as a daemon and interact with it via supervisorctl `/usr/bin/supervisorctl`.  Crawlers is the name of a group that each crawler process is part of ( see supervisor.conf ).  We can start these individually ( which we never do ), or we can start and stop the entire group of them with the commands below.  The syntax crawlers:* is the group name, followed by a colon and a star to indicate all the processes in that group.
- start all processes in a group `/usr/bin/supervisorctl start crawlers:*`
- gracefully stop all processes in a group - `/usr/bin/supervisorctl stop crawlers:*`

### Load a new .conf file:
Restart supervisord with this command: `sudo /usr/bin/supervisorctl reload`

### Control Processes from the CLI
- start crawler_0: `/usr/bin/supervisorctl start crawlers:crawler_0`
- stop all crawlers: `/usr/bin/supervisorctl stop crawlers:*`


install supervisord
-------------------
- `sudo easy_install supervisor`
- if a supervisord.conf file is in the repo for the current project, you must create a symlink to it from /etc/supervisord.conf ( there will be no supervisord.conf file palced in /etc/ when supervisor is installed )
  - eg. `sudo ln -s /data/www/directory/configs/supervisor/supervisord.conf /etc/supervisord.conf`
- run supervisord: `sudo /usr/bin/supervisord`
- logs are stored in /tmp ( controlled via the .conf file )
