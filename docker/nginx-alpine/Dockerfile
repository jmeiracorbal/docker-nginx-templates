FROM nginx:stable-alpine

# add user www-data to image: based debian images
# RUN addgroup -g 1000 -S www-data && adduser -u 1000 -D -S -G www-data www-data

# add user www-data based alpine images and ensure www-data user exists
RUN set -x ; \
  addgroup -g 82 -S www-data ; \
  adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1

RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

# add to link to modules folder
RUN ln -s /etc/nginx/modules /usr/lib/nginx/modules
