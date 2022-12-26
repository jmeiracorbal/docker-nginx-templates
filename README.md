# nginx dockerized boilerplates

## Requirements

Domain DNS services must be configured previously. Certbot makes a certificate requesting... If you don't have any, certbot launch an error:

Example with indagapp.xyz without any DNS Server configured previously:

```text
Requesting a certificate for indagapp.xyz

Certbot failed to authenticate some domains (authenticator: webroot). The Certificate Authority reported these problems:
  Domain: indagapp.xyz
  Type:   dns
  Detail: DNS problem: NXDOMAIN looking up A for indagapp.xyz - check that a DNS record exists for this domain; DNS problem: NXDOMAIN looking up AAAA for indagapp.xyz - check that a DNS record exists for this domain

Hint: The Certificate Authority failed to download the temporary challenge files created by Certbot. Ensure that the listed domains serve their content from the provided --webroot-path/-w and that files created there can be downloaded from the internet.

Some challenges have failed.
Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
ERROR: 1
```

## Run ./init-letsencrypt.sh

Its needed to create dummy certificate for domains: `./init-letsencrypt.sh mydomain.xyz www.mydomain.xyz`
