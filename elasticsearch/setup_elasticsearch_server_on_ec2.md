Setup Elasticsearch Server on EC2 Instance
==========================================
- **This is for old pre V1.0 install**
- probably want to migrate to a much newer version of elasticsearch next time we use it


setup tutorial
--------------
*this is somewhat outdated but the best resource when starting from scratch*
- use this blog post "elasticsearch on ec2" to do basic install of servers
- http://www.elasticsearch.org/tutorials/2011/08/22/elasticsearch-on-ec2.html


another great setup tutorial
----------------------------
- https://gist.github.com/910301


setup the ec2 instance
----------------------
- boot up an amazon ec2 instance
	- **ES will not run on a micro instance.** There is not enough memory for the JVM ( Java Virtual Machine )
- AWS EC2 Security Groups
	- use the elasticsearch_node group
	- or ... create a new one with these settings
		- open ssh port 22
		- open port 9200 ( we used to open 9300, but tbj doesn't know why, doesn't seem to matter )
		- limit the IP addresses to
			- your local machine
			- the elastic IP of the API for that ES node
			- the private IP of the API for that ES node ( if you're connecting through the ES Private IP )
		- You will probably have to update the Local IP from time to time
- alter the boot drive
	- increase the size of the ephemeral boot drive
		- we usually use 50GB, which is plenty for most moderately sized indexes
			- we use 250 in production as of 05/20/14
		- we only need a single EBS drive for ES nodes
	- set the drive to persist on termination of the instance
		- this can help with increased speed to get an index back up later
		- although ... ES recovers really fast even on large intances
	- you must run `sudo resize2fs /dev/sda1` to resize the root partition once logged in
		- this takes about 1min per 10gb
- add a password for ec2-user ( do not setup ssh login )
	- this password lets us execute things as the elasticsearch user ( see "Add Elasticsearch User" section )
	- `sudo passwd ec2-user`
- update all packages on server
	- `sudo yum update`


install elasticsearch
---------------------
- **latest version:** `http://www.elasticsearch.org/download/`
- wget the latest version
	- *version numbers will have to be updated from the examples below*

```shell
wget http://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.20.2.zip
sudo unzip elasticsearch-0.20.2.zip -d /usr/local/elasticsearch
```

- move all files from the elasticsearch-0.20.2 folder into the /var/usr/elasticsearch/ folder
- delete the elasticsearch-0.20.2 folder


install plugins
---------------

### install aws cloud gateway
*this gateway allows es to use s3 to store a persistent backup of the index*
- check latest version here: https://github.com/elasticsearch/elasticsearch-cloud-aws
- `sudo /usr/local/elasticsearch/bin/plugin -install elasticsearch/elasticsearch-cloud-aws/1.10.0`

### install head
*web monitor like phpmyadmin but for ES*
- installation instructions: https://github.com/mobz/elasticsearch-head
	- preferred method is to install as a plugin
- `sudo /usr/local/elasticsearch/bin/plugin -install mobz/elasticsearch-head`
- once es is started, head will be accessible at: http://ec2-address.amazonaws.com:9200/_plugin/head/

### install attachment mapper
*allows file attachments within an es index*
*eg. used for attaching html and pdf files to our `files` within the `companies` index*
- check latest version here: https://github.com/elasticsearch/elasticsearch-mapper-attachments
- `sudo /usr/local/elasticsearch/bin/plugin -install elasticsearch/elasticsearch-mapper-attachments/1.9.0`
	- use the latest version

### Install BigDesk
*profiling elasticsearch nodes and clusters*
- `sudo /usr/local/elasticsearch/bin/plugin -install lukas-vlcek/bigdesk`
- website: http://bigdesk.org/#elasticsearch_plugin
- check version of bigdesk to install based on the version of ES
	- https://github.com/lukas-vlcek/bigdesk
- once es is started, bigdesk will be accessible at: http://ec2-address.amazonaws.com:9200/_plugin/bigdesk/


Configure ElasticSearch
-----------------------

### Elasticsearch General Config
- create new elasticsearch.yml file
- `vim elasticsearch.yml`
	- **see the elasticsearch.yml.example in this documentation folder**

### Logging
- logging is enabled by default
- limit to just `warn` and higher ( instead of `info` )
	- `info` puts too much data in the logs
	- `sudo vim /usr/local/elasticsearch/config/logging.yml`
	- change this line `es.logger.level: INFO` to `es.logger.level: WARN`


Create Elasticsearch User
-------------------------

### Why?
- run es as the 'elasticsearch' user
- we have to do this to increase open file limits on aws ec2 instances
- tutorial: https://github.com/wuhaixing/doc.elasticsearch.cn/blob/master/tutorials/_posts/2011-02-22-running-elasticsearch-as-a-non-root-user.textile

### How?
- create elasticsearch user with elasticearch directory as home:
  - `sudo useradd -d /usr/local/elasticsearch -s /bin/sh elasticsearch`
- change user permissions for elasticsearch install directory
	- `sudo chown -R elasticsearch /usr/local/elasticsearch`
- edit `/etc/security/limits.conf` and add the following lines at the top to increase the number of open files the elasticsearch user can have
	- `elasticsearch soft nofile 64000`
	- `elasticsearch hard nofile 64000`
	- `elasticsearch memlock unlimited`
- TBJ thinks you have to log out and log back in for these settings to take effect


Controlling ES
--------------
- swtich to elasticsearch user: `sudo su elasticsearch`
- start elasticsearch `ES_HEAP_SIZE=4g /usr/local/elasticsearch/bin/elasticsearch`
  - the ES_HEAP_SIZE cannot be pre-set without the service wrapper ( TBJ can't fine where to set this )
  - it must be specified at launch
  - set to half the avaialble ram
  - this setting replaces setting these individually ( ES_MIN_MEM & ES_MAX_MEM )
- using the service wrapper ( optional, and not recomended by TBJ )
  - after booting an AMI, install the service wrapper and interact with ES that way
	- `sudo /usr/local/elasticsearch/bin/service/elasticsearch64 install`
	
	
Setting Up New Indexes
======================
*when doing this, a freshly booted node is currently going to join the companies cluster. this may cause performance issues when the companies index gets big.  this can be solved by changing the es node image so it launch es at boot ( it currently launches as a service on boot )*

- boot an instance from the most current es node image in ec2
	- set the tag 'index' to the name of the new index ( eg. teg => nsns )
- after logging in to the new instance
	- stop elasticsearch ( sudo /usr/local/elasticsearch/bin/service/elasticsearch stop )
	- delete all files from the es:
		- data folder ( sudo rm -r /usr/local/elasticsearch/data/* )
		- logs folder ( sudo rm /usr/local/elasticsearch/logs/* )
	- edit the es config file with the new index data ( sudo vim /usr/local/elasticsearch/config/elasticsearch.yml )
		- change the cluster name ( should be "index_name-cluster" )
		- change the tags.index to the new index name ( eg. nsns or companies )
		- change the gateway.s3.buckt to the new bucket ( should be "elasticsearch-index_name-index")
			- make sure to create the bucket in s3 if it doesn't exist already


Run as a Service ( optional ) 
=============================
- this is probably out of date, as it was last run through on version 0.2x
- runs on boot and adds some easier controls and settings*
- generally, tbj does not recommend this ... it causes problems managing es servers


Install as a Service
--------------------

### download the service wrapper
- download and unzip the latest service wrapper to your home directory
- move the service code to the elasticsearch `bin/` directory
- give execution permisions to the service wrapper install files

```
cd
sudo wget https://github.com/elasticsearch/elasticsearch-servicewrapper/zipball/master
sudo unzip master
sudo mv elasticsearch-elasticsearch-servicewrapper-3e0b23d/service/ /usr/local/elasticsearch/bin/
sudo chmod a+x /usr/local/elasticsearch/bin/service/elasticsearch32 /usr/local/elasticsearch/bin/service/elasticsearch64
```

### edits to bin/service/elasticsearch.conf
- `sudo vim /usr/local/elasticsearch/bin/service/elasticsearch.conf`
- update to set es install path
	- `set.default.ES_HOME=/usr/local/elasticsearch`
- update to limit log files
	- `wrapper.logfile.maxsize=100`
	- `wrapper.logfile.maxfiles=10`

### edits to service/elasticsearch
- `sudo vim /usr/local/elasticsearch/bin/service/elasticsearch`	
	- uncomment the `RUN_AS_USER` line and change to `RUN_AS_USER=elasticsearch`
	- uncomment the `ULIMIT_N=` line and change to `ULIMIT_N=32000`
	- alter the `LOCKDIR` line and change to `LOCKDIR="$ES_HOME/lock"`
- create the lock directory ( `sudo mkdir /usr/local/elasticsearch/lock` )
	
### The Service Wrapper
- Notes: https://github.com/elasticsearch/elasticsearch-servicewrapper
- set elasticsearch to start on boot ( as a daemon )
	- this also allows control via `sudo service elasticsearch`
	- `sudo /usr/local/elasticsearch/bin/service/elasticsearch64 install`
- remove service wrapper ( so ES doesn't start on boot )
	- `sudo /usr/local/elasticsearch/bin/service/elasticsearch64 remove`
- make sure it's working
	- `sudo /usr/local/elasticsearch/bin/service/elasticsearch start`

Controlling ES as a Service
---------------------------
- `sudo elasticsearch service elasticsearch start|stop|restart|status|help`
  - since elasticsearch is set to run as user `elasticsearch`, you don't need to start it as user `elasticsearch`
  - to execute commands as user `elasticsearch` use `sudo su elasticsearch`
- if everything in this setup guide was followed, Elasticsearch Service should start on boot
