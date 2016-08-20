xDebug & WebGrind
=================

Setup XDebug
------------
*debugging tool for php, also displays pretty html errors and arrays*
*this should probably be installed on every system we install php on*
- `sudo yum install php-pecl-xdebug`
- update `php.ini` so that `html_errors = On`
- restart httpd ( `sudo /etc/init.d/httpd restart` )
- output more depth from `var_dump()`
	- update php.ini ( `xdebug.var_display_max_depth = #` ... like 5, default 3 )

webgrind ( web based gui for xdebug )
--------
- download from - http://code.google.com/p/webgrind
	- extract into the web directory
	- make sure these lines are set in php.ini for xdebug ( webgrind will auto check the xdebug.profiler_output_dir for profiles )

```shell
zend_extension=/usr/lib64/php/modules/xdebug.so
xdebug.profiler_enable = 1
xdebug.profiler_output_dir = /tmp/xdebug
```
- create /tmp/xdebug
	- give webserver ownership of /tmp/xdebug
- restart webserver
	- this is tested with apache and lighttpd
