# Domain-name-configuration
A Guide on how to setup a domain name that points to your website
## What you will need
In order to setup a domain name that points to your website you will need the following:
- a server at your home (housing) or a cloud server (hosting)
- your DNS or a cloud DNS
- certbot for HTTPS certificates
## The goal
The final goal is that the domain *example.domain.com* points to the website on my server.
### Home Server configuration
I will host my website on my linux server at home. The server runs Apache as webserver and has an IP address.. let's say *192.168.1.11*. I have my website inside the folder */var/www/html*. At the moment, if I type on any computer in my LAN the address 192.168.1.11 I will see my website. If I want to make it visible outside the LAN I will have to open the port 80 of the router and make it redirect to my server *192.168.1.11*. After the configuration, requests from outside the LAN will be redirected to my website. Now let's make a step further and use domain names instead of IPs.
#### Virtualhosts configuration
Apache Virtual Hosts allows hosting multiple websites on a single server using a single IP address. Simply put, if you have a single web server but want to host multiple websites, you can use Virtual Hosts to make the web server appear to host multiple independent servers.
Instead of serving only one website in the default folder */var/www/html/* now I can serve multiple websites.





