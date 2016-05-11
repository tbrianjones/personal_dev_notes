IAM Roles
=========
- **these are a mess to deal with ... try to avoid for small scale stuff**


user login
----------
- users are created by tbj in his aws account
- users can login to tbj's aws account here:
	- https://jones-aws.signin.aws.amazon.com/console


configuring roles ( users )
---------------------------
- users and groups can be created in the IAM section of the AWS Management Console
- users can be given specific privileges to access anything in the AWS system
	- create policies to give specific privileges to a user using the AWS Policy Generator: http://awspolicygen.s3.amazonaws.com/policygen.html
	- then paste that policy into the specific user's permissions in the IAM section of the AWS Management Console


policy notes
------------
- when applying actions to buckets
	- use this resource: `arn:aws:s3:::bucket-name`
	- these resources don't work: `arn:aws:s3:::bucket-name/`, `arn:aws:s3:::bucket-name/*`
- when applying actions to directory-objects within buckets:
	- use this resource: `arn:aws:s3:::bucket-name`/directory-name/*`
	- these resources don't work: `arn:aws:s3:::bucket-name`/directory-name`, `arn:aws:s3:::bucket-name`/directory-name/`


example policy
--------------
*used to give user 'jones' complete access to the some folders in the S3 bucket 'example-bucket'*  
- what these policies do:
	- list all buckets
	- list all objects in a specific bucket
	- do most general stuff to all objects within specific folders
	
```
{
  "Statement": [
    {
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::*"
    },
    {
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::example-bucket"
    },
    {
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:GetObjectVersion",
        "s3:DeleteObject",
        "s3:DeleteObjectVersion"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::example-bucket/nsns/drawings/*",
        "arn:aws:s3:::example-bucket/nsns/images/*"
      ]
    }
  ]
}
```
