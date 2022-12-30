# NGINX Alpine dockerized

This project is builded with [h5bp server configs](https://github.com/h5bp/server-configs-nginx.git) with configuration snippets for better server performance and security strategies.
If you need to create a letsencrypt certs with certbot this projects uses a modificated bash script from [pentacent](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71).

# Requirements

* Domain DNS Server with internet access.
* A server with internet access over the installation is made.
* Don't touch the project structure except `templates`
* Domain name does end with a valid public suffix (TLD)

## Create web containers with letsencrypt

Domain DNS services must be configured previously. Certbot makes a certificate requesting... If you don't have any, certbot launch an error:
Example with app.xyz without any DNS Server configured previously:

$ `./letsencrypt app.xyz www.app.xyz` # we can add more domains

If server DNS is not config correctly or without internet access:

```text
Requesting a certificate for app.xyz (change app.xyz by your domain)

Certbot failed to authenticate some domains (authenticator: webroot). The Certificate Authority reported these problems:
  Domain: indagapp.xyz
  Type:   dns
  Detail: DNS problem: NXDOMAIN looking up A for indagapp.xyz - check that a DNS record exists for this domain; DNS problem: NXDOMAIN looking up AAAA for indagapp.xyz - check that a DNS record exists for this domain

Hint: The Certificate Authority failed to download the temporary challenge files created by Certbot. Ensure that the listed domains serve their content from the provided --webroot-path/-w and that files created there can be downloaded from the internet.

Some challenges have failed.
Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
ERROR: 1
```

## Project structure

This repository has the following structure based on h5bp nginx but h5bp has snippets was moved to custom.d folder:

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

# Reference

* [Pentacent letsencrypt docker](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71)
* [h5bp server configs for servers](https://github.com/h5bp/server-configs-nginx.git)
* [Fix error user www-data: getpwnam("www") failed in /etc/nginx/nginx.conf](http://blog.tobiasforkel.de/en/2016/09/10/nginx-docker-container-and-getpwnamwww-data-problem/)
* [Build docker container from docker image](https://docs.docker.com/compose/compose-file/build/#illustrative-sample)
* [Wrong permissions in volume in Docker container](https://stackoverflow.com/a/59879136)
* [How to Redirect HTTP to HTTPS in Nginx](https://phoenixnap.com/kb/redirect-http-to-https-nginx)
