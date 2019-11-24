---
title: Setting Apache Virtual Host
layout: post
categories:
- devops
---

#### Install Apache WebServer (Pre-Req)

```bash
sudo apt-get update
sudo apt-get install apache2
```

### Apache Virtual Host
To run more than one site on a single machine, you need to setup virtual hosts for sites your plan to host on an apache server.

#### Name Based Virtual Hosts (Most Common)
  * The server relies on the client to report the hostname as part of the HTTP headers. Using this technique, many different hosts can share the same IP address.

#### IP Based Virtual Hosts
  * IP-based virtual hosting is a method to apply different directives based on the IP address and port a request is received on.
  * Most commonly, this is used to serve different websites on different ports or interfaces.

---
### Setting up Virtual Directories

```bash
sudo mkdir -p /var/www/aayushtuladhar.com/public_html
sudo chown -R $USER:$USER /var/www/aayushtuladhar.com/public_html
```

### Creating Virtual Host Files

```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/aayushtuladhar.com.conf

<VirtualHost *:80>
    ServerAdmin admin@aayushtuladhar.com
    ServerName aayushtuladhar.com
    ServerAlias aayushtuladhar.com *.aayushtuladhar.com
    DocumentRoot /var/www/aayshtuladhar.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

* ServerName - Base Domain that should match for the virtual host definition.
* ServerAlias - Lists names are other names which people can use to see that same web site.

---

### Enable Virtual Host
`sudo a2ensite example.com.conf`

### Disable Default Virtual Host
`sudo a2dissite 000-default.conf`

### Restart Apache

```
sudo service apache2 restart    # Ubuntu 15.10
sudo systemctl restart apache2  # Ubuntu 14.10 and Earlier
```

### Reference
- http://www.unixmen.com/setup-apache-virtual-hosts-on-ubuntu-15-10/
- https://httpd.apache.org/docs/2.4/vhosts/name-based.html
