# Kubernetes Configurations

1. Decoupling Configuration from Code:

    I. Improves flexibility and reusability.

    II. Enables environment-specific configurations.

2. Managing Sensitive Information:

    I. Ensures security and compliance.

## ConfigMaps
Store Non-Confidential Data:

Key-value pairs for application settings.
Examples: Database URLs, feature flags.

## Secrets
Store Sensitive Information:

Encoded in base64 to prevent direct exposure.
Examples: Passwords, tokens, keys.

## Commands

### ConfigMap Command

##### Get Configmap
```bash
    kubectl get configmap -n <NAMESPACE>
```
##### Get Configmap in yaml
```bash
    kubectl get configmap -n <NAMESPACE> -o yaml
```
##### Edit Configmap in yaml
```bash
    kubectl edit configmap <NAME> -n <NAMESPACE>
```

##### Delete Configmap in yaml
```bash
    kubectl delete configmap <NAME> -n <NAMESPACE>
```

### Secret Command

##### Get Secrets
```bash
    kubectl get secret -n <NAMESPACE>
```
##### Get Secret in yaml
```bash
    kubectl get secret <NAME> -n <NAMESPACE> -o yaml
```
##### Edit Secret in yaml
```bash
    kubectl edit secret <NAME> -n <NAMESPACE> -o yaml
```

##### Get Secret in yaml
```bash
    kubectl delete secret <NAME> -n <NAMESPACE> -o yaml
```

## Practical I

#### Configmap Manifest

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
data:
  database_url: jdbc:mysql://localhost:3306/mydb
  database_user: admin
  database_password: admin123

```
#### Pod Manifest 
```bash
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod1
spec:
  containers:
    - name: example-container
      image: busybox
      env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: database_url
        - name: DATABASE_USER
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: database_user
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: example-secret
              key: database_password
      command: ["sh", "-c", "while true; do echo DATABASE_URL=$DATABASE_URL; echo DATABASE_USER=$DATABASE_USER; echo DATABASE_PASSWORD=$DATABASE_PASSWORD; sleep 3600; done"]
  restartPolicy: Always

```

### Practice II

#### Secrets Manifest
```bash
apiVersion: v1
kind: Secret
metadata:
  name: practice-secret
type: Opaque
data:
  database_password: YWRtaW4xMjM= # admin123 in base64

```

#### Pod Manifest
```bash
apiVersion: v1
kind: Pod
metadata:
  name: practice-pod2
spec:
  containers:
    - name: practice-container
      image: busybox
      env:
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: practice-secret
              key: database_password
      command: ["sh", "-c", "while true; do echo $DATABASE_PASSWORD; sleep 3600; done"]
  restartPolicy: Never
```

### Practice III

#### Configmap and Secret Combine

#### Pod Manifest
```bash
apiVersion: v1
kind: Pod
metadata:
  name: combine-pod
spec:
  containers:
    - name: example-container
      image: busybox
      env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: database_url
        - name: DATABASE_USER
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: database_user
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: practice-secret
              key: database_password
      command: ["sh", "-c", "while true; do echo $DATABASE_URL && echo $DATABASE_USER && echo $DATABASE_PASSWORD; sleep 3600; done"]
  restartPolicy: Never

```


## References:
  
*https://kubernetes.io/docs/concepts/configuration/configmap/*

*https://kubernetes.io/docs/concepts/configuration/secret/*

*https://kubernetes.io/docs/reference/kubectl/*