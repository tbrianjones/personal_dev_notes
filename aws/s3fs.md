S3 File System (S3FS)
=====================
S3FS is an S3 backed file system allowing you to mount an S3 bucket as a drive on an EC2 instance (or other linux instance).
- github repo: https://github.com/s3fs-fuse/s3fs-fuse
- github wiki: https://github.com/s3fs-fuse/s3fs-fuse/wiki/Installation-Notes

Setup
-----

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
  - `sudo yum install -y gcc libstdc++-devel gcc-c++ fuse fuse-devel curl-devel libxml2-devel mailcap automake openssl-devel git`
- isntall and compile s3fs

```
git clone https://github.com/s3fs-fuse/s3fs-fuse
cd s3fs-fuse/
./autogen.sh
./configure --prefix=/usr --with-openssl # See (*1)
make
sudo make install
```

- make sure that last line is executed if you perform them all at once
- create a folder to mount the s3 drive to `/some/folder`
- launch s3fs
  - `sudo /usr/local/bin/s3fs bucket-name /some/folder -o iam_role=iam_role_name -o allow_other -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5`
  - s3fs won't be available with `sudo` (not sure how to enable this)
  - s3fs must be called directly with it's full filepath `/usr/local/bin/s3fs`
  - see this article for some settings in this command
    - http://stackoverflow.com/questions/23939179/ftp-sftp-access-to-an-amazon-s3-bucket#23946418

S3FS should be installed and working. The folder works like any other mounted drive folder. It's only viewable via root unless you change permissions, which you should not if you're setting up an FTP server in front of this.

### Launch S3FS on boot/reboot
- add the desired launch command to `/etc/rc.local` and save it. done.

### Alternate Ways to Launch S3FS
- launch with IAM User credentials, rather than an IAM EC2 Role
    - put credentials in a file `access_key:secret_access_key` > `/etc/psswd-s3fs`
    - `sudo /usr/local/bin/s3fs bucket-name /some/folder -o passwd_file=/etc/passwd-s3fs -o allow_other -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5`
- debug (append `-d -d -f -o f2 -o curldbg`)
    - eg. `sudo /usr/local/bin/s3fs bucket-name /some/folder -o iam_role=iam_role_name -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5 -d -d -f -o f2 -o curldbg`

### References
- http://stackoverflow.com/questions/23939179/ftp-sftp-access-to-an-amazon-s3-bucket#23946418
- https://github.com/s3fs-fuse/s3fs-fuse
- https://cloudacademy.com/aws-certifications-training/
