Setup EBS Volumes
=================
- **should probably upgrade to EXT4 file system**
- persistent aws drives attached to ec2 instances



Resize the Ephemeral Drive ( root volume )
-----------------------------------------
- increase the drive size when launching or through the EBS console
	- set drive to persist on termination of the instance
- resize the root partition
	- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html
	- usually this:
		- `growpart /dev/xvda 1`
		- `sudo resize2fs /dev/xvda1`


Attach and Mount an new EBS volume
----------------------------------
*here is a detailed tutorial http://www.coderchris.com/linux/how-to-mount-an-amazon-ebs-disk-as-a-drive-in-linux-centos/2011/01/31*
- simplified tutorial from above
	- format the new drive after it's attached from within the aws console ( sudo mkfs.ext3 /dev/sdf )
	- mount the drive to the proper folder on the system ( sudo mount /data/ )


Attach and Mount an existing EBS Volume
---------------------------------------
- right click the volume you want to attach, and click 'attach volume'
- select the EC2 instance you want to attach to
- enter the correct device path ( this should default to the correct entry )
	- usually '/dev/sdf' for the first drive
	- increment, sdf, sdg, sdh... for subequent drives
- click 'yes, attach'
- wait for drive to say 'attached' in 'EBS Volumes' console
	- initially says 'attaching'
	- this can take a few minutes
- make a directory to mount the drive to
	- sudo mkdir /directory/
	- generally use /data/ or /data2/
- edit fstab config file
	- sudo vim /etc/fstab ( use sudo to edit read only file )
	- append this line to end of file 
		- `/dev/sdf    /data       ext3    noatime      0   0`
		- use the correct device path - eg. /dev/sdf... sdg... sdh
- sudo mount /directory/ ( mounts the drive )
- drive should be working


Detach an EBS Volume
--------------------
- sudo umount /dev/partition_name ( usually something like /dev/xdvf )
- then click detach drive in the aws management console
- the drive typically takes a few minutes to detach
- when you can't figure out how to shut down all processes relying on this drive ( this can result in bad things )
	- shut down ec2 instance volume is attached to ( stop )
	- right click the volume in the EBS Volumes control panel and click "detach valume"
