---
#Namespace: This part defines a Kubernetes namespace named "game-2048". 
apiVersion: v1
kind: Namespace
metadata:
  name: game_2048
---
#Deployment: This part defines a Deployment resource named "deployment-2048" within the "game-2048" namespace. 
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: game_2048
  name: deployment_2048
  labels:
    app: game_2048
spec:
  replicas: 5
  selector:
    matchLabels:
      app.kubernetes.io/name: game_2048
  template:
    metadata:
      labels:
        app.kubernetes.io/name: game_2048
    spec:
      containers:
      - name: game_2048
        image: public.ecr.aws/l6m2t8p7/docker-2048:latest
        ports:
        - containerPort: 80
---
#Service: This part defines a Service named "service-2048" within the "game-2048" namespace.
apiVersion: v1
kind: Service
metadata:
  namespace: game_2048
  name: service_2048
spec:
  type: NodePort 
  selector:
    app.kubernetes.io/name: game_2048
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
---
#Ingress_Controller:This section defines an Ingress resource named "ingress-2048" within the "game-2048" namespace. 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: game_2048
  name: ingress_2048
  annotations:
#Allows adding extra configuration or metadata to the Ingress. Here, annotations are used to configure an AWS Application Load Balancer (ALB).
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
#Specifies the class of the Ingress controller to use. In this case, it's configured to use an ALB Ingress controller.
  ingressClassName: alb
  rules:
    http:
      paths:
        path: /
        backend:
          service:
            name: service_2048
            port:
              number: 80