# Docker Swarm security

Namespace and cgruop provide isolation to containers

Isolation limits the impact of certain exploits or privilege escalation attacks. If one container is compromised, it is less likely that it can be used to gain any further control outside the containers.


## Docker daemon attack surface
Allow trusted user to access the docker daemon.

## Linux kernel capabilities
Capabilities provide fine-tune what containers and process can access.

This means a process can run as root inside a container but does not ahave access to do everything root could normally do on the host

Eg: docker uses net_bind_service capability to allow container to process to bind to a port below 1024 without running as root.

## Encrypting overlay network
\You can encrypt communication between containers on overlay network in order to provide greater securtiy within your swarm


### Create encrypted network
use --opt encrypted
```
docker network create --opt encrypted --driver overlay my-encrypted network
```

## MTLS (Mutually Authenticated TLS) in docker swarm
Docker swarm provides additional security by encrypting communication between various components in the cluster

Here both participants in communication exchange certificates and all communication is authenticated and encrypted

Enabled by default

## Securing Docker daemon socket
By default docker not exposed to network by default

1. Create a CA
2. Create server and client certificate
3. Configure daemon on the server to use tlsverify mode
4. Configure client to connect securely using the client certificate

```
openssl genrsa -aes256 -out ca-key.pem 4096
openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem -subj "/C=US/ST=Texas/L=Keller/O=Linux Academy/OU=Content/CN=$HOSTNAME"
openssl genrsa -out server-key.pem 4096
openssl req -subj "/CN=$HOSTNAME" -sha256 -new -key server-key.pem -out server.csr
echo subjectAltName = DNS:$HOSTNAME,IP:<server private IP>,IP:127.0.0.1 >> extfile.cnf
echo extendedKeyUsage = serverAuth >> extfile.cnf
openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out server-cert.pem -extfile extfile.cnf
```


Then generate the client certificates:
```
openssl genrsa -out key.pem 4096
openssl req -subj '/CN=client' -new -key key.pem -out client.csr
echo extendedKeyUsage = clientAuth > extfile-client.cnf
openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out cert.pem -extfile extfile-client.cnf
```


Set appropriate permissions on the certificate files:
```
chmod -v 0400 ca-key.pem key.pem server-key.pem
chmod -v 0444 ca.pem server-cert.pem cert.pemSet appropriate permissions on the certificate files:
chmod -v 0400 ca-key.pem key.pem server-key.pem
chmod -v 0444 ca.pem server-cert.pem cert.pem
```

Configure your Docker host to use tlsverify mode with the certificates that were created earlier:
```
sudo vi /etc/docker/daemon.json
{
  "tlsverify": true,
  "tlscacert": "/home/cloud_user/ca.pem",
  "tlscert": "/home/cloud_user/server-cert.pem",
  "tlskey": "/home/cloud_user/server-key.pem"
}
```

Look for the line that begins with ExecStart and change the -H so that it looks like this:
```
sudo vi /lib/systemd/system/docker.service


ExecStart=/usr/bin/dockerd -H=0.0.0.0:2376 --containerd=/run/containerd/containerd.sock
sudo systemctl daemon-reload
sudo systemctl restart docker
```

Copy the CA cert and client certificate files to the client machine:
```
scp ca.pem cert.pem key.pem cloud_user@<client private IP>:/home/cloud_user
```

On the client machine, configure the client to securely connect to the remote Docker daemon:
```
mkdir -pv ~/.docker
cp -v {ca,cert,key}.pem ~/.docker
export DOCKER_HOST=tcp://<docker server private IP>:2376 DOCKER_TLS_VERIFY=1
```

Test the connection:
```
docker version
```