HTTPS / SSL on AWS EC2
======================

### Setup

- Install SSL via Step 1 in the AWS tutorial below
  - This is all you need ot do to secure the connection over https if you don't need a verified connection with a little green lock in the browser. Customers won't trust this though.
- Gen A CSR to paste into your SSL Cert Provider (I usually use GoDaddy)
  - `sudo openssl req -new -key /etc/pki/tls/private/localhost.key -out csr.pem`
  - On EC2, you can use `localhost.key` that is already generated here: `/etc/pki/tls/private/`
  - 




*USEFUL ARTICLES*
- Amazon EC2 Apache SSL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-an-instance.html
- Rando Artcile on SSL and EC2: http://jafty.com/blog/installing-godaddy-ssl-certificate-on-amazon-ec2/
