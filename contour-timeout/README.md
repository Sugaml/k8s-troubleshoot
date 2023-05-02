# Project Contour Timeout (Networking Troubleshoot)
## To test the response timeout for an Ingress resource in a local Kubernetes cluster, you can follow these steps:

1. Start a local Kubernetes cluster using a tool such as Minikube or Kind.

2. Create a deployment and a service for a simple application. Here's an example of a deployment and a service that expose a simple HTTP endpoint:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  replicas: 1
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx
          ports:
            - name: http
              containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  selector:
    app: my-app
  ports:
    - name: http
      port: 80
      targetPort: http
```
    
3. Create an Ingress resource that routes traffic to the service. Here's an example of an Ingress resource that routes traffic to the my-app service:

```

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    projectcontour.io/response-timeout: "10s"
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /my-path
            pathType: Prefix
            backend:
              service:
                name: my-app
                port:
                  name: http
```

In this example, we've set the response timeout to 10 seconds using the projectcontour.io/response-timeout annotation.

Use a tool such as curl to send requests to the application and measure the response time. Here's an example of a curl command that sends a request to the application:


$ curl -v http://example.com/my-path

This command sends a request to the example.com/my-path endpoint defined in the Ingress resource. The -v option enables verbose output, which includes the response time.

Increase the response time beyond the configured timeout to test the timeout behavior. You can do this by adding a delay to the application using a tool such as sleep. Here's an example of a command that adds a 15-second delay to the application:


$ kubectl exec -it my-app-<pod-name> -- /bin/sh -c 'sleep 15'

Replace <pod-name> with the name of the pod running the my-app container.

Send another request to the application and observe the timeout behavior. If the response time exceeds the configured timeout, Contour will terminate the connection and return an error response to the client.

By following these steps, you can test the response timeout for an Ingress resource in a local Kubernetes cluster using Contour.