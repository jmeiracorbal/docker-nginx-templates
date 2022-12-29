# NGINX Alpine dockerized

This project is builded with [h5bp server configs](https://github.com/h5bp/server-configs-nginx.git) with configuration snippets for better server performance and security strategies.
If you need to create a letsencrypt certs with certbot this projects uses a modificated bash script from [pentacent](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71).

# Requirements

* Domain DNS Server with internet access.
* A server with internet access over the installation is made.
* Don't touch the project structure except `templates`

## Create certs

Domain DNS services must be configured previously. Certbot makes a certificate requesting... If you don't have any, certbot launch an error:
Example with app.xyz without any DNS Server configured previously:

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

## Create letsencrypt certs

Its needed to create dummy certificate for domains: `./init-letsencrypt.sh mydomain.xyz www.mydomain.xyz`

# Reference

* [pentacent letsencrypt docker](https://pentacent.medium.com/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71)
* [h5bp server configs for servers](https://github.com/h5bp/server-configs-nginx.git)
