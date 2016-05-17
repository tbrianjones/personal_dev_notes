S3 File System (S3FS)
=====================
https://github.com/s3fs-fuse/s3fs-fuse

Setup
-----

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

### launch ec2
- launch an instance and associate your IAM role on launch
  - this can't be done/changed after launch
  - only one iam role can be associated with an ec2

### Install S3FS
- install dependencies
  - `sudo yum install automake fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel`
- isntall and compile s3fs

```
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install
```
- reboot instance (won't have access to s3fs until you reboot)
- launch s3fs
  - `s3fs nwd-sftp /sftp -o iam_role=sftp-server -o stat_cache_expire=10 -o enable_noobj_cache -o enable_content_md5`
