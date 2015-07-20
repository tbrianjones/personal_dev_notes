Setup MongoDB Server on EC2 Instance
==========================================
*as of 01/01/13*

### Create a YUM package config:
- see details from mongo: http://docs.mongodb.org/manual/tutorial/install-mongodb-on-redhat-centos-or-fedora-linux/
- `sudo vim /etc/yum.repos.d/10gen.repo`

### Then insert the following text into that file and save:
    [10gen]
    name=10gen Repository
    baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
    gpgcheck=0
    enabled=1

### Intall MongoDB
- `sudo yum install mongo-10gen mongo-10gen-server`

### Install the Mongo PHP API:
- Install php and required components, a c compiler, and make:
- `sudo yum install php php-pear php-devel gcc-c++ make`

### Install the mongo php api extension for php:
- `sudo peclinstall mongo`

### Add this line to php.ini, then restart httpd or whatever is running php:
- `extension=mongo.so`

### The PHP Mongo API Docs:
- http://www.php.net/manual/en/mongo.tutorial.php