# custom nginx image with custom index.html
FROM ubuntu:bionic

ENV NGINX_VERSION 1.14.0-0ubuntu1.3

RUN apt-get update && apt-get install -y curl
RUN apt-get update && apt-get install -y nginx

WORKDIR /var/www/html

# add cab download fiel and archive it
ADD index.html ./

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

# it uses this signal to stop the container
STOPSIGNAL SIGTERM
HEALTHCHECK CMD curl localhost:80