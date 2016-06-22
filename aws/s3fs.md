S3 File System (S3FS)
=====================
S3FS is an S3 backed file system allowing you to mount an S3 bucket as a drive on an EC2 instance (or other linux instance).
- github repo: https://github.com/s3fs-fuse/s3fs-fuse
- github wiki: https://github.com/s3fs-fuse/s3fs-fuse/wiki/Installation-Notes

Setup on Amazon Linux
---------------------

### Create S3 Bucket
- create a bucket `bucket-name`

### Create IAM Role
- you must create an IAM Role and associate the role with the EC2 instance that's going to mount the S3 Bucket
- You could also use IAM Credentials (key and secret key), but this is not as secure (sometimes required during dev though)
- create a policy with the desired access

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::bucket-name"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::bucket-name/*"
        }
    ]
}
```

- create a roll and associate the policy with it
- there are no keys to manage with roles, as the ec2 instance will fetch them itself each time (they rotate)

### Launch EC2
- launch an instance and associate your IAM role on launch
  - this can't be done/changed after launch
  - only one iam role can be associated with an ec2

### Install S3FS
- install dependencies
  - `sudo yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel`
- isntall and compile s3fs
  - make sure that last line is executed if you perform them all at once

```
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse/
./autogen.sh
./configure --prefix=/usr --with-openssl # See (*1)
make
sudo make install
```

- create `/etc/fuse.conf`
  - add a single line with this value: `user_allow_other`
  - save and close (s3fs will have to be run as root to access this file)
- create a folder to mount the s3 drive to `/some/folder`
- launch s3fs
  - `sudo s3fs sftpBucket:/ /some/folder/ -o url=https://s3.amazonaws.com -o iam_role=ftp-server -o endpoint=us-west-2 -o allow_other -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5 -o umask=022`
  - `-o` settings
    - `-o umask=022`: give propper permissions to bucket folders so sftp users can access them
    - `-0 allow_other`: is a mounting parameter that gives access to the mounted folder in some way
    - http://stackoverflow.com/questions/23939179/ftp-sftp-access-to-an-amazon-s3-bucket#23946418

S3FS should be installed and working. The folder works like any other mounted drive folder. It's only viewable via root unless you change permissions, which you should not if you're setting up an FTP server in front of this.

### Launch S3FS on boot/reboot
- add the desired launch command to `/etc/rc.local` and save it. done.

### Alternate Ways to Launch S3FS
- launch using fstab
    - `sftpBucket:/userName/ /home/userName/files/ fuse.s3fs _netdev,url=https://s3.amazonaws.com,iam_role=ftp-server,endpoint=us-west-2,allow_other,stat_cache_expire=10,enable_noobj_cache,enable_content_md5,umask=022,uid=501 0 0`
- launch with IAM User credentials, rather than an IAM EC2 Role
    - put credentials in a file `access_key:secret_access_key` > `/etc/psswd-s3fs`
    - `sudo s3fs bucket-name /some/folder -o passwd_file=/etc/passwd-s3fs -o allow_other -o umask=022 -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5`
- debug (append `-d -d -f -o f2 -o curldbg`)
    - eg. `sudo s3fs bucket-name /some/folder -o iam_role=iam_role_name -o allow_other -o umask=022 -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5 -d -d -f -o f2 -o curldbg`

### Performance Metrics
- 10mb/s (testing copying 300mb file to an S3 mounted folder)

### References
- http://stackoverflow.com/questions/23939179/ftp-sftp-access-to-an-amazon-s3-bucket#23946418
- https://github.com/s3fs-fuse/s3fs-fuse
- https://cloudacademy.com/aws-certifications-training/
