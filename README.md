# certbot

Forked to work more happily on EC2 boxes so I don't need to keep following [this medium post](https://medium.com/@andrenakkurt/great-guide-thanks-for-putting-this-together-gifford-nowland-c3ce0ea2455) which is basically impossible on a remote node.

Let's Encrypt is a wonderful service and hopefully someone other than me will find this helpful.

## Steps to use:
1. Install Python:
    
    `yum install python27-devel git`

2. Install Let’s Encrypt (from my repo): 

    `git clone https://github.com/michaelalbinson/letsencrypt /opt/letsencrypt`

3. Run the installer: 

    `/opt/letsencrypt/letsencrypt-auto --debug`
    
4. Make a configuration file (/etc/letsencrypt/config.ini) that will be used to sign all future certificates and renewals with your private key and email address:

   ``` 
    $ echo "rsa-key-size = 4096" >> /etc/letsencrypt/config.ini
    $ echo "email = ________@____.com" >> /etc/letsencrypt/config.ini
   ```

5. Generate certificates:
    
    `$ sudo /opt/letsencrypt/certbot-auto certonly --webroot -w /var/www/html -d ________.com -d www.________.com`
    
    Generally `/var/www/html` is your webroot, but see these notes from [this medium post](https://medium.com/@andrenakkurt/great-guide-thanks-for-putting-this-together-gifford-nowland-c3ce0ea2455)
    
    > `/var/www/html` is your webroot (default for Apache used here; modify if yours is different or you’re using a different server such as nginx)
      ________.com is your domain that lives in above webroot (you can also request a certificate for multiple domains if your EC2 runs subdomains or multiple domains)

6. Manually Renew certificates:

    1. Turn off your web server so port 80 is available `nginx -s stop`
    
    2. Run `sudo /opt/letsencrypt/letsencrypt-auto --no-bootstrap renew`
    
    - OR

7. Automatically Renew Certificates (I've had mixed results with this):

    1. `sudo nano /etc/crontab`
    
    2. ```
      # Renew SSL Certs
      0  13  *  *  *  ec2-user  /opt/letsencrypt/letsencrypt-auto --no-bootstrap renew
      
      # Restart nginx
      10 13  *  *  *  root      nginx -s restart 
      ```

Credit:
- [Medium post 1](https://medium.com/@gnowland/deploying-lets-encrypt-on-an-amazon-linux-ami-ec2-instance-f8e2e8f4fc1f)
- [Medium post 2](https://medium.com/@andrenakkurt/great-guide-thanks-for-putting-this-together-gifford-nowland-c3ce0ea2455)
- [Let's Encrypt Issue #5455](https://github.com/certbot/certbot/issues/5455)
- [Amazon's Docs for certbot and Apache](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html#letsencrypt)
    - I don't use this because personally I prefer nginx, but to each their own!
