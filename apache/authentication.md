authentication
==============


basic authentication
--------------------
*this is used to create a login popup within the browser that requires a username and password to access the directory for which the directives are set within the apache config file*
- httpd v2.2 ( apache ) default config file location: /etc/httpd/conf/httpd.conf
- directives to add to httpd.conf:

``` 
# add the auth directives inside the directory that you want to protect
# in this case, we're protecting the entire web directory
<Directory "/var/www/html">
  AuthType basic
  AuthName "Username and Password Required"
  AuthUserFile /var/www/users
  Require valid-user
</Directory>
```

### Create a New Users File ( and first user )
- create a `users` file ( defined in AuthUserFile directive )
  - htpasswd -c /var/www/users username
    - sometimes htpasswd will have to be called directly from the httpd / apache folder
    - this has been on other distro's of linux, or hosted servers
    - /usr/local/apache ... sometimes
  - it will then prompt you for a password ( twice )
  - looking at the users file will show a username and an encrypted password

- after these changes have been made, restart httpd ( sudo /etc/init.d/http restart )

### Create New Users ( after users file has already been created )
- `sudo htpasswd /var/www/users new_user_name` ( no -c )
- enter password at prompts

### Delete users
- `sudo htpasswd -D /var/www/users existing_user_name`
