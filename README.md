# URL-Shortener
Load Balanced URL Shortener using Docker, Kubernetes and Redis

# What is the Project about?
Developed a production-ready URL shortening service using Flask and Redis, containerized with Docker and deployed on Kubernetes. Features include horizontal pod autoscaling, NGINX load balancing, comprehensive health monitoring, and CORS-enabled REST API. Implemented robust error handling, configurable environment management through ConfigMaps/Secrets, and stress testing capabilities to ensure high availability and performance under load.

# Features

- Shorten URLs – Convert long URLs into short, shareable links.

- Kubernetes Deployment – Runs in a K8s cluster with Redis for caching.

- Horizontal Pod Autoscaling (HPA) – Automatically scales based on traffic.

- Ingress Routing – Custom domain support (e.g., short.ly).

- Load Testing – Includes a Python script for stress testing.

- Dockerized – Containerized for easy deployment.


# Prerequirements 
1. MiniKube
2. Kubectl
3. Docker
4. Python 3.9+

# Project Directory
```
.
├── app.py
├── docker-compose.yml
├── Dockerfile
├── kubernetes
│   ├── configs
│   │   ├── config-map.yaml
│   │   └── secrets.yaml
│   ├── deployments
│   │   ├── redis-deployment.yaml
│   │   └── web-deployment.yaml
│   ├── hpa
│   │   └── web-hpa.yaml
│   ├── ingress
│   │   └── ingress.yaml
│   └── services
│       ├── redis-service.yaml
│       └── web-service.yaml
├── README.md
├── requirements.txt
└── stress_test.py

6 directories, 14 files
```

# How to run the Project

Step 1: Start MiniKube, build the image and transfer the image to Minikube
```
minikube start
eval $(minikube docker-env -u)
docker build -t url-shortener_web:latest .
minikube image load url-shortener_web:latest
```

Step 2: Deploy Kubernetes Resources
```
kubectl apply -f kubernetes/configs/
kubectl apply -f kubernetes/deployments/
kubectl apply -f kubernetes/services/
kubectl apply -f kubernetes/ingress/
kubectl apply -f kubernetes/hpa/
```

Step 3: Use the URL-Shortener
```
curl -X POST -H "Host: short.ly" -H "Content-Type: application/json" \
-d '{"url":"https://example.com"}' \
http://$(minikube ip)/shorten
```

Expected Output:
```
{"code":"bms7um","original_url":"https://example.com","short_url":"http://short.ly/bms7um"}
```

Step 4: Stress Testing
```
python stress_test.py \
--api "http://$(minikube ip):xxxxx/shorten" \
--requests 1000 \
--concurrency 50
```

Expected Output:
```
Testing endpoint: http://192.168.58.2:31538/shorten
Sending 1000 requests with 50 concurrent workers

Submitted 100 requests...
Submitted 200 requests...
Submitted 300 requests...
Submitted 400 requests...
Submitted 500 requests...
Submitted 600 requests...
Submitted 700 requests...
Submitted 800 requests...
Submitted 900 requests...
Submitted 1000 requests...

Results:
- Successes: 1000/1000
- Duration: 11.35 seconds
- Rate: 88.12 requests/sec
- Success rate: 100.0%
```

**NOTE**:

What to substitute for xxxxx?
Run the below command:
```
kubectl get svc
```
Look for your service url-shortener and confirm:

5000:xxxxx/TCP under PORT(S) and use xxxxx above
