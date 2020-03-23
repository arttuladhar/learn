---
title: 09 - ConfigMaps and Secrets
---
- [ConfigMaps](#configmaps)
  - [Creating ConfigMaps](#creating-configmaps)
    - [Using CommandLine](#using-commandline)
    - [Using Configuration File](#using-configuration-file)
    - [Using Properties File](#using-properties-file)
  - [Using ConfigMaps Inside Pods](#using-configmaps-inside-pods)
    - [As Environment Variable](#as-environment-variable)
    - [As Volume](#as-volume)
- [Secrets](#secrets)
  - [Creating Secret](#creating-secret)
  - [Create a Secret from Literal and Display Its Details](#create-a-secret-from-literal-and-display-its-details)
  - [Create a Secret from YAML File](#create-a-secret-from-yaml-file)
    - [Use Secrets Inside Pods](#use-secrets-inside-pods)

## ConfigMaps

ConfigMaps allow us to decouple the configuration details from the container image. Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes. We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.

### Creating ConfigMaps

#### Using CommandLine

```shell
kubectl create configmap my-config \
    --from-literal=key1=value1 \
    --from-literal=key2=value2
```

#### Using Configuration File

```shell
apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.
```

#### Using Properties File

*permission-reset.properties*

```
permission=read-only
allowed="true"
resetCount=3
```

```
kubectl create configmap permission-config --from-file=<path/to/>permission-reset.properties
```

### Using ConfigMaps Inside Pods

#### As Environment Variable

Inside a Container, we can retrieve the key-value data of an entire ConfigMap or the values of specific ConfigMap keys as environment variables.

```yaml
  containers:
  - name: myapp-full-container
    image: myapp
    envFrom:
    - configMapRef:
      name: full-config-map
```

```yaml
  containers:
  - name: myapp-specific-container
    image: myapp
    env:
    - name: SPECIFIC_ENV_VAR1
      valueFrom:
        configMapKeyRef:
          name: config-map-1
          key: SPECIFIC_DATA
    - name: SPECIFIC_ENV_VAR2
      valueFrom:
        configMapKeyRef:
          name: config-map-2
          key: SPECIFIC_INFO
```

#### As Volume

We can mount a vol-config-map ConfigMap as a Volume inside a Pod. For each key in the ConfigMap, a file gets created in the mount path (where the file is named with the key name) and the content of that file becomes the respective key's value:

```yaml
  containers:
  - name: myapp-vol-container
    image: myapp
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: vol-config-map
```

## Secrets

Secret object can help by allowing us to encode the sensitive information before sharing it. With Secrets, we can share sensitive information like passwords, tokens, or keys in the form of key-value pairs, similar to ConfigMaps; thus, we can control how the information in a Secret is used, reducing the risk for accidental exposures. In Deployments or other resources, the Secret object is referenced, without exposing its content.

{{% notice info %}}
It is important to keep in mind that the Secret data is stored as plain text inside etcd, therefore administrators must limit access to the API server and etcd. A newer feature allows for Secret data to be encrypted at rest while it is stored in etcd; a feature which needs to be enabled at the API server level.
{{% /notice %}}

### Creating Secret

### Create a Secret from Literal and Display Its Details

```shell
# Create Secret
kubectl create secret generic my-password --from-literal=password=mysqlpassword

# Get Secret
kubectl get secret my-password

# Describe Secret
kubectl describe secret my-password
```

### Create a Secret from YAML File

```
echo mysqlpassword | base64
bXlzcWxwYXNzd29yZAo=
```

*mypass.yaml*
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=
```

{{% notice warning %}}
Please note that base64 encoding does not mean encryption, and anyone can easily decode our encoded data
{{% /notice %}}

*mypass.yaml*
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
stringData:
  password: mysqlpassword
```

```shell
# Create Secret from YAML
kubectl create -f mypass.yaml
```

#### Use Secrets Inside Pods

**Using Secrets as Environment Variables**

```yaml
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
```

**Using Secrets as Files from a Pod**

```yaml
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-data"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-password
```
