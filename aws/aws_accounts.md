AWS Accounts - General Info
===========================

Set up New AWS Account and Link to Master Account
-------------------------------------------------
These notes are for setting up a new account for a new project or new engineer to have a dev playground.

### Create The Account
- create account
  - usually have to clear cache and cookies on browser to get to a signup link, or use a different browser
- create account using person's work email, or an email for that project

### Create Rolls so Master Account Users can watch/admin
- sign in to the new account
- create master account "watching" roll within the new account
	- create new iam role
	  - use the 'roll' for "cross-account access between account you own"
	  - enter id of master account and require mfa (if using mfa on master account)
	  - select "ReadOnlyAccess" policy
	  - create roll
- create master account "admin" roll within the new account
	- same as "watching" above, but select "AdministrationAccess" policy, instead of "ReadOnlyAccess"
- create policies in master account to allow users to watch and admin new account
	- root account or account with admin privies will not need this

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "sts:AssumeRole",
        "Resource": "arn:aws:iam::337837119756:role/role-name-in-new-account"
    }
}
```

- create a group that contains each of the new policies in the main account
- assign users who need access to these new groups

### Other Important Stuff To Do
- enable cloudtrail logs (to track anything that might happen)
- enable IAM user access to billing information
	- go to "my account" in top right dropdown
	- this allows IAM users/roles to access billing info in the sub accounts
- setup billing alerts on the new account so we know if it goes too high

### Setup The New User (If there is one)
- have user install "google authenticator" on their phone
- setup mfa for new account
- setup mfa for master account
