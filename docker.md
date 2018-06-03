Docker
======

AWS Notes
---------
- is using ECS and ECR, yuo probably want to launch the AWS EC2 marketplace AMI `Amazon ECS-Optimized Amazon Linux AMI`
- search when launching an image to get this optimized and configured image (rather than the standard AWS Linux)

Setup & Install
---------------
sudo yum install docker
sudo service docker start

List Containers
---------------
docker -ps (list running containers)
docker -a -ps (list all containers)
