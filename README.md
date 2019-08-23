# Ansbile for dockerized Wordpress with Nginx as a reverse proxy and Letsencrypt as SSL-certs issuer

This script is needed to get on your host a bunch of Wordpress instances in one place that are accessible via reverse proxy (nginx) and uses SSL (via letsencrypt). 
Reverse proxy with SSL is supposed to be a separate server with corresponding sudoers.d to perform certificate delivery and nginx reload. It's supposed
that you already have installed and set up nginx and letsencrypt.
It's also supposed that you have a common resolver for these two nodes. If you don't have one, you should change variables in host_vars folder to be correct.

## Install prerequisites (needed only at the first launch)

```
ansible-playbook -K install_prerequisites.yml
```

## Setup /etc/sudoers.d/vmmaster file (needed only at the first launch)

```
vmmaster ALL=(ALL:ALL) NOPASSWD: /usr/sbin/service nginx reload
vmmaster ALL=(ALL:ALL) NOPASSWD: /usr/bin/certbot

```

## Create the directory for nginx configs (needed only at the first lauch)

```
sudo mkdir /etc/nginx/generated-wordpress/
sudo chown -R vmmaster:vmmaster /etc/nginx/generated-wordpress/
```

## Setup nginx.conf
Add the following string in http section of nginx.conf

```include /etc/nginx/generated-wordpress/*;```

## Choose available user-space port / Look for the last used port by docker

```docker ps```

## Choose a name for your wordpress and a port to use (this is the single line you really need here)

```ansible-playbook ./play.yml ./inventory.yml -e "wp_instance_name=WORDPRESS_SITE_NAME wp_port=PORT_NUMBER"```

After this you should see your site at WORDPRESS_SITE_NAME.domain

## (optional) Install theme for wordpress

```
cd /opt/wordpress-sites/WORDPRESS_SITE_NAME/wp-content/themes
sudo -u www-data wget <theme_url>
sudo -u www-data unzip <theme_filename.zip>
```
