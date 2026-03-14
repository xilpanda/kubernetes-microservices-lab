# Kubernetes Microservices Lab – CKA Preparation

## Overview

This project demonstrates a ****production-like Kubernetes microservices architecture**** built as part of my preparation for the ****Certified Kubernetes Administrator (CKA)**** certification.

The objective of this lab is to understand how core Kubernetes primitives interact in a real environment and how applications are deployed, exposed and debugged inside a cluster.

The project demonstrates practical usage of:

-   Deployments
-   Pods
-   Services
-   Ingress routing
-   Container networking
-   Pod lifecycle management
-   Kubernetes troubleshooting

The environment simulates a simple ****multi-service application stack**** consisting of:

-   frontend service
-   API backend
-   Redis datastore

  

# Architecture

The application architecture deployed inside the Kubernetes cluster:

                        Internet  
                            │  
                            │  
                     Ingress Controller  
                      (NGINX Ingress)  
                            │  
                            │  
                       app.local  
                            │  
               ┌────────────┴────────────┐  
               │                         │  
           Frontend                  /api route  
            (nginx)                      │  
                                         │  
                                     API service  
                                 (http-echo container)  
                                         │  
                                         │  
                                       Redis  
                                   (data storage)

Routing behaviour:

app.local        → frontend service  
app.local/api    → api service

  

# Technologies Used

-   Kubernetes
-   kubectl
-   NGINX
-   Redis
-   Hashicorp http-echo container
-   Ingress Controller
-   Calico CNI networking

  

# Project Structure

Example repository layout:

kubernetes-microservices-lab  
│  
├── README.md  
│  
├── manifests  
│   ├── frontend-deployment.yaml  
│   ├── api-deployment.yaml  
│   ├── redis-deployment.yaml  
│   ├── services.yaml  
│   └── ingress.yaml  
│  
├── diagrams  
│   └── architecture.md  
│  
├── troubleshooting  
│   └── pod-debugging.md  
│  
└── scripts  
    └── deploy.sh

  

# Components

## Frontend

A simple NGINX container used to simulate a web frontend.

Deployment example:

kubectl create deployment frontend --image=nginx  
kubectl expose deployment frontend --port=80

Service type:

ClusterIP

  

## API Service

The backend API is simulated using the ****Hashicorp http-echo container****.

Deployment:

kubectl create deployment api --image=hashicorp/http-echo  
kubectl expose deployment api --port=5678

This service returns a simple response to simulate backend API behaviour.

Test request:

curl -H "Host: app.local" http://NODE\_IP:INGRESS\_PORT/api

Expected response:

hello-world

  

## Redis Backend

Redis is deployed as the datastore layer.

Deployment:

kubectl create deployment redis --image=redis  
kubectl expose deployment redis --port=6379

Testing Redis:

kubectl exec -it redis-pod -- redis-cli

Example:

SET test hello  
GET test

Result:

"hello"

  

# Ingress Routing

Ingress is used to expose multiple services through a single entry point.

Example configuration:

rules:  
\- host: app.local  
  http:  
    paths:  
    - path: /  
      pathType: Prefix  
      backend:  
        service:  
          name: frontend  
          port:  
            number: 80  
  
    - path: /api  
      pathType: Prefix  
      backend:  
        service:  
          name: api  
          port:  
            number: 5678

This allows:

app.local        → frontend  
app.local/api    → api backend

  

# Kubernetes Concepts Demonstrated

## Self-Healing Infrastructure

Kubernetes automatically recreates failed pods to maintain the desired state.

Example:

kubectl delete pod redis-xxxx

Kubernetes automatically recreates the pod because it is managed by a Deployment controller.

Controller hierarchy:

Deployment  
   ↓  
ReplicaSet  
   ↓  
Pods

This behaviour is known as ****reconciliation loop****.

  

## Service Networking

Services provide stable networking endpoints for pods.

Example services used in this lab:

frontend  → ClusterIP  
api       → ClusterIP  
redis     → ClusterIP

Pods communicate internally using service DNS names.

Example:

redis:6379  
api:5678

  

# Pod Troubleshooting

Common debugging commands used in this lab.

Check pods:

kubectl get pods

Describe pod:

kubectl describe pod <pod-name>

View logs:

kubectl logs <pod-name>

Execute commands inside container:

kubectl exec -it <pod-name> -- /bin/sh

  

# Stateless vs Stateful Workloads

The lab demonstrates the difference between ****stateless and stateful workloads****.

### Stateless workloads

Examples:

-   frontend
-   api

Pods can be restarted without losing data.

  

### Stateful workloads

Example:

-   Redis

Since Redis currently runs without persistent storage, deleting the pod results in data loss.

Production environments would typically use:

PersistentVolume  
PersistentVolumeClaim  
StatefulSet

  

# Example Pod Lifecycle Test

Delete Redis pod:

kubectl delete pod redis-xxxx

Watch Kubernetes recreate it:

kubectl get pods -w

This demonstrates Kubernetes self-healing behaviour.

  

# What This Lab Demonstrates

This project demonstrates hands-on experience with:

-   Kubernetes deployments
-   container orchestration
-   service networking
-   ingress routing
-   pod lifecycle management
-   cluster troubleshooting

The lab simulates a ****basic production-like microservices environment**** and serves as a practical learning exercise for the ****CKA certification****.

  

# Possible Improvements

Future enhancements may include:

-   Persistent storage for Redis
-   Horizontal Pod Autoscaler (HPA)
-   Resource limits and requests
-   Liveness and readiness probes
-   Helm chart packaging
-   Monitoring with Prometheus and Grafana

# GitHub portfolio project created as part of hands-on Kubernetes learning and CKA preparation.
