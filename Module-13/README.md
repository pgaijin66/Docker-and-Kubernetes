# Docker registires
Docker resistry is responsible for storing and distributing docker images.

By default docker pulls image from public registry which is docker hub.

You can also create your own registries using Docker's open source registry software or Docker Trusted Registry, non-free enterorsuse solution.

To create a a basic registry, you need to run container using the registry image and publish port 6000

### Login to docker registry
```
docker login # by default it logs into hub.docker.com
```
or 
```
docker login www.lytestregistry.com # provide custom registry url
```

### Create docker registry
```
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

### Turn up the log level on registry
```
docker logs registry
```

Default log level is info. To change it
```
docker run -d -p 5000:5000 --restart=always --name registry -e REGISTRY_LOG_LEVEL=debug registry:2
```

Now check logs using 
```
docker logs registry
```

### Registry security
Create two folder 
1. auth
2. certs

By default registy is open, we need to turn on authentication and we also also want to activate TLS for HTTPS.
```
docker run --entrypoint htpasswd registry:2 -Bbn testuser password > auth/htpasswd
```

create a self signed certificate
```
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt

Generating a 4096 bit RSA private key
.........++
..........................................................................................++
writing new private key to 'certs/domain.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:AU
State or Province Name (full name) []:NSW
Locality Name (eg, city) []:SYD
Organization Name (eg, company) []:AUDINATE
Organizational Unit Name (eg, section) []:AUDINATE
Common Name (eg, fully qualified host name) []:test.domain.com
Email Address []:
```

Now run registry container with additional parameters
```
docker run -d -p 443:443 --restart=always --name registry \
  -v ./registry/certs:/certs \
  -v ./registry/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2
```

### Test registry security
```
curl -k https://localhost:443
```

### Using docker registries

There are multiple ways  to use a registry with a self-signed certificate
1. Turn off certificate verification (very insecure).
2. Provide the public certificate to the docker enginer.

### Pull/Push to private ergistry
To pull and push images from your private registry, use tag the image with the registry hostname (optionally)
```
REGISTRY_PUBLIC_HOSTNAME/IMAGE_NAME:TAG
```
Login to docker registry
```
docker login RESOTRYTORY_HOSTNAME
```
and push
```
docker push IMAGE_NAME
```