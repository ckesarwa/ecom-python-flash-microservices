#Project structure
ecom-python-flash-microservices/
├── order-service/
│   ├── mysql-order-service-deployment
│   ├── order-service-deployment
│   ├── requirements.txt
│   ├── Dockerfile
│   └── app.py
└── product-service
    ├── mysql-product-service-deployment
	├── product-service-deployment
    ├── requirements.txt
    ├── Dockerfile
    └── app.py

#COMMANDS

1# Go to the dockerfile path
cd C:\Work\CODING\Python_Project\ecom-python-flash-microservices\

2# Build Docker Images
docker build -t product-service:latest ./product-service
docker build -t order-service:latest ./order-service

3#Start minikube
minikube start

4# Docker Login
docker login

5# Push Docker Images to a Registry
docker tag product-service:latest ckesarwa/product-service:latest
docker push ckesarwa/product-service:latest     //need to make sure its public

docker tag order-service:latest ckesarwa/order-service:latest
docker push ckesarwa/order-service:latest     //need to make sure its public

6# Deploy MySQL on Kubernetes

kubectl apply -f ./product-service/mysql-product-service-deployment.yaml
kubectl apply -f ./order-service/mysql-order-service-deployment.yaml


7# Database command
// On mysql-product pod
mysql -u root -p

CREATE DATABASE IF NOT EXISTS productdb;
USE productdb;

CREATE TABLE IF NOT EXISTS products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
	stock INT DEFAULT 0,
    price DECIMAL(10, 2) NOT NULL
);

// On mysql-order pod
mysql -u root -p

CREATE DATABASE IF NOT EXISTS orderdb;
USE orderdb;

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    product_id INT,
    quantity INT
);

8# Deploy Microservices
kubectl apply -f ./product-service/product-service-deployment.yaml
kubectl apply -f ./order-service/order-service-deployment.yaml

9# Forward ports for local testing
kubectl port-forward service/product-service 5000:5000
kubectl port-forward service/order-service 5001:5001

10# Open minikube dashboard
minikube dashboard
