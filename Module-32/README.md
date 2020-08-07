## K8s secrects

SUpport your app on pod needs to connect to MySQL DB

Generally creds are hardcoded into the container image or pass as environment variable.

A lot of risk associated with this:
1. Access to creds
2. dev needs to have cred of prod system
3. Update of credential wil cause to create new docker image.

### use secrets

Credentials are stored in central secret store. You store your credentials here. When ever a application pod runs, it fetches credentials from screct store and use it to authenticate to database.

Advantage:
1. Do not store creds on the pod
2. Dev do not need to have password of prod system
3. Update of credentials do not needs docker image to be rebuilt.

### What is Central Secret Store.

This is k8s secret store. 

Secret is an objec that contains small amount of sensitive data such as passwd, token or key

It is stored in ETCd database.

Other secret manager can be used like AWS secret manager, AWS system parameter store, hashicorp vault etc

### secret
You need three things
1. TYPE => can be generic(from file, directory or literal value), docker registry and TLS
2. NAME
3. DATA
```
kubectl create secret TYPE NAME DATA
```

### creating new secrect
```
kubectl create secret generic firstsecret --from-literal=dbpass=mypassword123

secret/firstsecret created
```

### Fetch secrets
```
kubectl get secrets
```

### To view more of secret
```
kubectl describe secret firstsecret


Name:         firstsecret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
dbpass:  13 bytes
```

### Get secret
```
kubectl get secret firstsecret -o yaml


apiVersion: v1
data:
  dbpass: bXlwYXNzd29yZDEyMw==
kind: Secret
metadata:
  creationTimestamp: "2020-08-07T04:01:29Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:dbpass: {}
      f:type: {}
    manager: kubectl
    operation: Update
    time: "2020-08-07T04:01:29Z"
  name: firstsecret
  namespace: default
  resourceVersion: "14846"
  selfLink: /api/v1/namespaces/default/secrets/firstsecret
  uid: 580391ad-49d0-4781-9874-760ec9aebb8d
type: Opaque
```

### Decode secret

It is base64 encoded, to decrypt you can sun 
```
echo "BASE64ENCODED" | base64 -d
```

### Using yaml
```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  # Username and password should be bas64 encoded
  username: ZGJhZG1pbg==
  password: MTIzNDUK
```

```
kubectl get secret                    

NAME                  TYPE                                  DATA   AGE
default-token-tbxbd   kubernetes.io/service-account-token   3      3h54m
firstsecret           Opaque                                1      9m45s
mysecret              Opaque                                2      31s
```

# Passing secret in container

Once create it is necessary to make it available to container in pod.

Two ways
1. use volumes

This way you can mount already created serect mounted to specific directory. We mounted secret to specific directory in container.
```
kubectl apply -f secretvolume.yaml
kubectl exec -it secretmount
cd /etc/foo
cat dbpass
```

2. Environment variable