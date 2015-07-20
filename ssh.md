SSH
===

###allow ssh login with password rather than keypair
- `sudo vim /etc/ssh/sshd_config`
    - set 'PasswordAuthentication' to 'yes'
- `sudo /etc/init.d/sshd reload` ( apply new settings )
- `sudo passwd ec2-user` ( then enter a password at prompt )
- you can now ssh with `ssh ec2-user@address.com`


###Logging into Private Servers on VPCs
- StackExchange Question: http://superuser.com/questions/826805/how-do-i-properly-ssh-into-a-server-behind-a-nat-while-maintaining-the-tightest/826897?noredirect=1#comment1085250_826897
- Edit your `/etc/ssh_config` file locally on your Mac and add these lines:

```
Host any_name_for_this_connection
        User ec2-user
        HostKeyAlias any_name_for_this_connection.www.website.com ( can be anything )
        HostName 54.164.25.211 ( host of NAT or server that you can SSH into )
        LocalForward 1025 10.0.1.43:22 ( local port to enable connection through | internal AWS IP of private server )
        IdentityFile ~/.aws/private_key.pem

Host another_connection_name
        User ec2-user
        HostKeyAlias another_connection_name.ec2.internal
        HostName localhost
        Port 1025
        IdentityFile ~/.aws/private_key.pem
```

- when connecting via terminal:
  1. Create the connection on port 1025 through the server you can SSH into: `ssh -C any_name_for_this_connection`
  2. Connect to the private server: `ssh another_connection_name`
- When connecting via a text editor like Coda or TextWrangler
  1. Create the connection on port 1025 as above.
  2. Then set the host in your text editor to localhost on port 1025.
