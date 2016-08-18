HTTPS / SSL on AWS EC2
======================

### Setup
*the process outlined in the Amazon Article in the Notes below is pretty complete, but has lot of extras steps that can be skipped.*

- Install SSL via Step 1 in the AWS tutorial below
  - This is all you need ot do to secure the connection over https if you don't need a verified connection with a little green lock in the browser. Customers won't trust this though.
- Gen A CSR to paste into your SSL Cert Provider (I usually use GoDaddy)
  - `sudo openssl req -new -key /etc/pki/tls/private/localhost.key -out csr.pem`
  - On EC2, you can use `localhost.key` that is already generated here: `/etc/pki/tls/private/`
  - Answer Questions based on Step 2, section 3 of the AWS Tutorial
- Create an SSL Cert in your provider and gen the host cert (I usually use GoDaddy)
  - In GoDaddy
    - go to SSL Certs
    - Create a new one or click manage on one you'd like to repurpose
    - Paste contents of `csr.pem` (created in the previous step) into 'Re-Key Cert'
    - Chat the site that the cert protects to the site you are protecting (eg. www.domain.com or subdomain.domain.com)
    - Don't change the encryption algo
    - save
    - go back to the cert pag and download the generated certs/keys as a zip
- Put the files in the GoDaddy Cert Zip here: `/etc/pki/tls/certs`
- Edit Apache SSL Config
  - `sudo vim /etc/httpd/conf.d/ssl.conf`
  - alter this line: `SSLCertificateFile /etc/pki/tls/certs/localhost.crt`
    - to be something like this: `SSLCertificateFile /etc/pki/tls/certs/f537d319b053e3bb.crt`
  - Don't change this line unless you didn't use your `localhost.key`
    - `SSLCertificateKeyFile /etc/pki/tls/private/localhost.key`
- Restart Apache and you should be good to go
  - if there are problems check here:
    - `/var/log/httpd/error_log`
    - `/etc/httpd/logs/error_logs`
- Security on EC2
  - Prevent HTTP access by blocking port 80 on the security group for this server
  - Allow HTTPS by granting access to port 443 ...

*USEFUL ARTICLES*
- Amazon EC2 Apache SSL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-an-instance.html
- Rando Artcile on SSL and EC2: http://jafty.com/blog/installing-godaddy-ssl-certificate-on-amazon-ec2/
