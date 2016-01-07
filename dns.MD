DNS Notes
=========

Email Configuration Best Practices
----------------------------------

### Setup an SPF Record
- info
  - complete notes: http://www.zytrax.com/books/dns/ch9/spf.html
  - short incomplete tutorial: http://nickwilsdon.com/spf-domain-records/
  - test a record: http://www.kitterman.com/spf/validate.html
- This is DNS record that tells requesting mailservers what IP's are ok to accept email from other than the IP in the headers of the sent email.
- Example record put in the TXT DNS config: v=spf1 ip4:210.5.24.40/32 -all
