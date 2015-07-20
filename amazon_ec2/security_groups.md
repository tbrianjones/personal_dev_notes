Security Groups on Amazon EC2
-----------------------------

- allow ssh login
	create ssh port access ( security groups >> create a new rule >> ssh )

- allow mysql access
	- create mysql networking port access ( security groups >> create a new rule >> mysql )

- allow http access
	- create http port access ( security groups >> create a new rule >> http )

- elasticsearch
	- custom tcp rules for ports 9200 and 9300
	- http, for access to head module

- allow webmin on port 10000
	create custom port access ( security groups >> create new rule >> create custom tcp rule
	set port to 10000

