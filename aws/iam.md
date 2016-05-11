AWS - IAM
=========
- handbook: http://docs.aws.amazon.com/IAM/latest/UserGuide/iam-ug.pdf
- faq: https://aws.amazon.com/iam/faqs/
- security
  - good 3rd party article: https://blog.codeship.com/aws-iam-security/
  - cross account access: http://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_cross-account-with-roles.html

### Root User
- never user root user keys for apps
- instead, create IAM users
- if you want an admin user, create a user with full privileges, then it's easy to revoke later if there are issues
- tutorial: http://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html
- overview: http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users 
