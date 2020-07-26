# Signing images and Docker content trust

## Docker content trust (DCT)
It provides a secure way to verify the integrity of images before you pull or run them on your systems

Using DCT the image creator signs each image with a certificate which clients can use to verify the iamge before running it.

Image and signature data is stored in docker registry

### Generate delegation key pair
This gives users access to sign images for a repository

Login to docker hub
```
docker login
```

create a delegation pair
```
docker trust key generate pgaijin66
```

Adding yourself as a signer to the repository
```
docker trust signer add --key pgaijin66.pub pgaijin66 pgaijin66/dct-test
```

### Enabling DCT
set DOCKER_CONTENT_TRUST environment variable 1
or add it in daemon.json


### Sign images
```
docker trust sign <repo>/<image><:tag>
```