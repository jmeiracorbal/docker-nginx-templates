# NGINX Alpine dockerized

This project is builded with [h5bp server configs](https://github.com/h5bp/server-configs-nginx.git) with configuration snippets for better server performance and security strategies.
If you need to create a letsencrypt certs with certbot this projects uses a modificated bash script from [pentacent](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71).

From h5bp all files and conf was extracted but not include some configurations like fastcgi.conf or fatcgi_params, required on php projects, for example:

Captured from `nginx:alpine-stable`:

```text
/etc/nginx # ls -l
total 36
drwxr-xr-x    1 root     root          4096 Dec 31 13:40 conf.d
-rw-r--r--    1 root     root          1077 Oct 19 10:49 fastcgi.conf
-rw-r--r--    1 root     root          1007 Oct 19 10:49 fastcgi_params
-rw-r--r--    1 root     root          5349 Oct 19 10:49 mime.types
lrwxrwxrwx    1 root     root            22 Nov 12 06:28 modules -> /usr/lib/nginx/modules
-rw-r--r--    1 root     root           648 Oct 19 10:49 nginx.conf
-rw-r--r--    1 root     root           636 Oct 19 10:49 scgi_params
-rw-r--r--    1 root     root           664 Oct 19 10:49 uwsgi_params
```

Included files from original image:

* -rw-r--r--    1 root     root          1077 Oct 19 10:49 fastcgi.conf
* -rw-r--r--    1 root     root          1007 Oct 19 10:49 fastcgi_params
* -rw-r--r--    1 root     root           664 Oct 19 10:49 uwsgi_params
* -rw-r--r--    1 root     root           636 Oct 19 10:49 scgi_params

The link to modules folder is not included too. On Dockerfile (`docker/nginx-alpine`) a link is created to add modules folder on the image builded. This image
is required on `docker-compose.yml` to build the `nginx-server-alpine` image.

The user www-data is not included too. The Dockerfile creates the users if not exists and add to the group www-data to get permissions over the volumes and the host:

```text
# add user www-data based alpine images and ensure www-data user exists
RUN set -x ; \
  addgroup -g 82 -S www-data ; \
  adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1

RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data
```

## Structure

This repository has the following structure based on h5bp snippets for nginx with some extras. The nginx folder is replaced on the pulled image.

```text
certbot
└── .../
log
└── ./nginx
templates
└── .../
docker-compose.yml
nginx
├── custom.d/
│   └── default.conf
├── h5bp/
│   ├── basic.conf
│   ├── location/
│   └── .../
├── conf.d/
│   └── .../
├── mime.types
└── nginx.conf
```

* custom.d: directives to complete nginx configuration. No server configuration.
* conf.d: encapsulated configuration. Example server {...} to define vhosts.

# Create sites with SSL Certs based on letsencrypt

## Requirements

* Domain DNS Server with internet access.
* A server with internet access over the installation is made.
* Don't touch the project structure except `templates`
* Domain name does end with a valid public suffix (TLD)

Domain DNS services must be configured previously. Certbot makes a certificate requesting... If you don't have any, certbot launch an error:
Example with app.xyz without any DNS Server configured previously:

$ `./letsencrypt app.xyz www.app.xyz` # we can add more domains

If server DNS is not config correctly or without internet access:

```text
Requesting a certificate for app.xyz (change app.xyz by your domain)

Certbot failed to authenticate some domains (authenticator: webroot). The Certificate Authority reported these problems:
  Domain: app.xyz
  Type:   dns
  Detail: DNS problem: NXDOMAIN looking up A for app.xyz - check that a DNS record exists for this domain; DNS problem: NXDOMAIN looking up AAAA for app.xyz - check that a DNS record exists for this domain

Hint: The Certificate Authority failed to download the temporary challenge files created by Certbot. Ensure that the listed domains serve their content from the provided --webroot-path/-w and that files created there can be downloaded from the internet.

Some challenges have failed.
Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
ERROR: 1
```

You need *.conf server files to include https certs:

* ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
* ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;

Optional, you can add more nginx options based on ssl:
* include /etc/letsencrypt/options-ssl-nginx.conf;
* ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

Add this options on 443 server. Replace `example.org` by your domain.

On the 80 port server add well-known validations:

```text
location /.well-known/acme-challenge/ {
    root /var/www/certbot;
}
```

A complete server conf file using with ssl:

```text
server {

    listen 80 default_server;
    # This will match any hostname
    server_name _;

    location / {
        return 301 https://$host$request_uri;
    }

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

}

server {

    listen 443 ssl;
    server_name example.org;

    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/html/public;

    ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;

    # nginx service validation: https://www.ssllabs.com/index.html
    # require run ./init-letsencrypt.sh
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://example.org; #for demo purposes
    }

}
```

Again, replace `example.org` with your domain.

# Using docker compose profiles

You can deploy another services using profile flag. Add more if you need. Example with added for php:

* `docker compose --profile php up`
* If you need to build previously: `docker compose --profile php up --build`

# Reference

* [Pentacent letsencrypt docker in less than 5 minutes](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71)
* [h5bp server configs for servers](https://github.com/h5bp/server-configs-nginx.git)
* [Fix error user www-data: getpwnam("www") failed in /etc/nginx/nginx.conf](http://blog.tobiasforkel.de/en/2016/09/10/nginx-docker-container-and-getpwnamwww-data-problem/)
* [Build docker container from docker image](https://docs.docker.com/compose/compose-file/build/#illustrative-sample)
* [Wrong permissions in volume in Docker container](https://stackoverflow.com/a/59879136)
* [How to Redirect HTTP to HTTPS in Nginx](https://phoenixnap.com/kb/redirect-http-to-https-nginx)
* [Workdir for docker-compose](https://stackoverflow.com/a/43290357)
* [Assigning profiles to services on docker compose](https://docs.docker.com/compose/profiles/#assigning-profiles-to-services)
* [Prevent to add docker compose prefix folder to network](https://stackoverflow.com/a/62101650)
* [Prevent ERROR: network {your_network} has active endpoints](https://stackoverflow.com/a/63034009)
* [nginx structure project to discover not included files on h5bp snippets](https://stackoverflow.com/q/46425926)
