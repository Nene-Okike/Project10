### LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

Task 1.
### Configure Nginx as a Load Balancer

1. Configure an EC2 VM based on Ubuntu Server 20.04 LTS and name it NginxLB.

 Open TCP port 80 for HTTP connections and port 443 for secured HTTPS connections.

 2. vi into /etc/hosts file and add  local DNS with Web Servers’ names.
  34.230.38.149 web1
  52.91.104.76 web2
  Escape, save and quit.

  3. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

Update the instance and Install Nginx

sudo apt update

sudo apt install nginx


Configure Nginx LB using Web Servers’ names defined in /etc/hosts by opening and modifying the default nginx configuration file thus:

sudo vi /etc/nginx/nginx.conf

Insert the following configuration into http section of the configuration file

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

Comment out this line in the configuration file

     include /etc/nginx/sites-enabled/*;

Restart Nginx and make sure the service is up and running by running 

sudo systemctl restart nginx

sudo systemctl status nginx

Task 2
Register a new domain name and configure secured connection using SSL/TLS certificates

1. Register a new domain name with any registrar of your choice. I registered doolings.tech with Bluehost

2. Create a Hosted zone in Route53 using the domain name just purchased from Bluehost.
Make sure the public hosted zone is selected and click create
3. Connect Route53 hosted zone to the Domain name by copying the name server from Route53 to the domain server we just created.

Click on the Domain name and click on DNS and select custom name server and copy each of the nameserver to each line and save.

4. Create A record in the hosted zone which will connect the loadbalancer with domain name and Hosted zone.

So we create two A records to point to our Load balancer IP

Connect to Web Servers from your browser using the new domain name using HTTP protocol – http://doolings.tech

5. Configure Nginx to recognize new domain name - doolings.tech

vi into nginx defaul configuration file and add

 server_name www.doolings.tech instead of server_name www.domain.com

 6. Install certbot and request for an SSL/TLS certificate

 1. run `sudo systemctl status snapd` to confirm that certbot is runnuing. 

 Then run `sudo snap install --classic certbot`

 Run the following command to request for certificate

 sudo ln -s /snap/bin/certbot /usr/bin/certbot

sudo certbot --nginx

Test secured access to your Web Solution by trying to reach 
https://doolings.tech


7. Set up periodical renewal of your SSL/TLS certificate

use this command to test for certificate renewal

Set up a cronjob renewal of the certificate by the crontab -e file with this command

* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1


sudo certbot renew --dry-run



