Docker
======

AWS Notes
---------
- if using ECS and ECR, it's suggested you launch the AWS EC2 Marketplace AMI `Amazon ECS-Optimized Amazon Linux AMI`
- search when launching an image to get this optimized and configured image (rather than the standard AWS Linux)

Setup & Install
---------------

### install

sudo yum install docker
sudo service docker start
sudo usermod -a -G docker ec2-user # add ec2-user to docker group so sudo is not needed

### start docker service

sudo service docker start


### test install

### Useful Tutorials
- aws ecs and docker: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html

List Containers
---------------
docker -ps (list running containers)
docker -a -ps (list all containers)
