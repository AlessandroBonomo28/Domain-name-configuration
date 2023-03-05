# Domain-name-configuration
A Guide on how to setup a domain name that points to your website
![cool_emoji](https://user-images.githubusercontent.com/75626033/222993456-342d1c33-e009-4d7c-883c-8bb027a14604.png)
## What you will need
In order to setup a domain name that points to your website you will need the following:
- a server at your home (housing) or a cloud server (hosting)
- DNS service
- certbot for HTTPS certificates
### Home server configuration
I will host my website on my linux server at home. The server runs Apache as webserver and has an IP address.. let's say *192.168.1.11*. I have my website inside the folder */var/www/html*. At the moment, if I type on any computer in my LAN the address 192.168.1.11 I will see my website. If I want to make it visible outside the LAN I will have to open the port 80 of the router and make it redirect to my server *192.168.1.11*. After the configuration, requests from outside the LAN will be redirected to my website. Now let's make a step further and use domain names instead of IPs.
### The hosts file
The "hosts" file is a configuration file used by the operating system to associate domain names with IP addresses. In other words, the hosts file is a way for the operating system to resolve domain names into IP addresses.

Typically, when you enter a domain name in a web browser, the browser sends a DNS request to find the IP address associated with that domain name. However, if the domain name is listed in the computer's hosts file, the operating system will use the IP address specified in the hosts file instead of sending a DNS request.

The hosts file can be used for various purposes, including:

- Testing a website before making it public, by associating a fake domain name with a local IP address.
- Blocking access to a specific website, by associating the domain name with an invalid IP address.
- Allowing access to a website that would otherwise be blocked by a firewall or DNS filter, by associating the domain name with an IP address that is not blocked.

In summary, the hosts file is a useful tool for local domain name resolution, and can be used for various practical purposes.
If you want the domain *example.domain.mine* to be redirected to 192.168.1.11 It should be configured like this
```
# /etc/hosts configuration
...

192.168.1.11 example.domain.mine
...
```
#### Virtualhosts configuration
Apache Virtual Hosts allows hosting multiple websites on a single server using a single IP address. Simply put, if you have a single web server but want to host multiple websites, you can use Virtual Hosts to make the web server appear to host multiple independent servers.
Instead of serving only one website in the default folder */var/www/html/* now I can serve multiple websites.
Let's make a virtualhost named *example.domain.mine* and let's suppose my website is inside the folder */var/www/mywebsite*
#### simple HTTP 80 virtualhost configuration
```
<VirtualHost *:80>
<IfModule php7_module>
    AddType application/x-httpd-php .php
    AddType application/x-httpd-php-source .phps
    <IfModule dir_module>
        DirectoryIndex index.html index.php
    </IfModule>
</IfModule>
    ServerAdmin pippo
    ServerName example.domain.mine
    ServerAlias example.domain.mine
    DocumentRoot /var/www/mywebsite
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
If I want to make a secure HTTPS server I will need **2 virtual hosts**: One for http (port 80) and another one for https (port 443). In addition I will need certbot for generating certificates (checkout [**link install certbot**](https://certbot.eff.org/)).
##### HTTP 80 rewrite to 443 virtualhost configuration
```
/etc/apache2/sites-available/example80.domain.mine

<VirtualHost *:80>
<IfModule php7_module>
    AddType application/x-httpd-php .php
    AddType application/x-httpd-php-source .phps
    <IfModule dir_module>
        DirectoryIndex index.html index.php
    </IfModule>
</IfModule>
    ServerAdmin pippo
    ServerName example.domain.mine
    ServerAlias example.domain.mine
    DocumentRoot /var/www/mywebsite
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    RewriteEngine on
    RewriteCond %{SERVER_NAME} =levelbuddy.bonomo.cloud
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```
##### HTTPS 443 virtualhost configuration
```
/etc/apache2/sites-available/example.domain.mine

<VirtualHost *:443>
<IfModule php7_module>
    AddType application/x-httpd-php .php
    AddType application/x-httpd-php-source .phps
    <IfModule dir_module>
        DirectoryIndex index.html index.php
    </IfModule>
</IfModule>
    ServerAdmin pippo
    ServerName example.domain.mine
    ServerAlias example.domain.mine
    DocumentRoot /var/www/mywebsite
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
RewriteEngine on
</VirtualHost>
```

After saving the virtualhosts inside the folder /etc/apache2/sites-available/. You will need to **ensite** them with apache.
> sudo a2ensite example.domain.mine

> sudo systemctl reload apache2

Now if you type in your browser "example.domain.mine" you will see your website. **Any issues may be caused by cache or the file /etc/hosts**
#### Adding certificates
In order to add certificate you will have to open certbot
> sudo certbot
- Pick your site and apply certificates

- Choose if you want to redirect http -> https

After adding the certificates to your virtualhost, these lines will be added to your site in */etc/apache2/sites-enabled/example.domain.mine*
```
Include /etc/letsencrypt/options-ssl-apache.conf
SSLCertificateFile /etc/letsencrypt/live/example.domain.mine/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/example.domain.mine/privkey.pem
```
Now if you type *http://example.domain.mine* you will be redirected to *https://example.domain.mine*
### Router configuration
You already forwarded port 80 traffic to your webserver, now you have to specify that the domain name example.domain.mine will be redirected to your webserver. After this configuration you will need one more step to be able to resolve your domain name from outside your LAN.
### DNS provider configuration
In my case the provider is Aruba and I will set a new domain name named *example.domain.mine* that points to my router IP. **Note that your router IP must be static or you will have to configure Dynamic IP update with CloudFlare**. Once you set the domain in the DNS provider panel wait for 15/10 min and then you should be good to go! Remember to **clear cache and check the hosts file of your browsing machine**

![maxresdefault](https://user-images.githubusercontent.com/75626033/222993541-efd76f0d-2ed0-4492-acf3-f8721a8e4008.jpg)


