---
title: Docker & Kubernetes - Déploiement et Orchestration
description: Bonnes pratiques de conteneurisation Docker multi-stage et manifestes Kubernetes
---

# Conteneurs & Orchestration Kubernetes

Cette fiche regroupe les standards appliqués pour la conteneurisation légère et sécurisée d'applications, ainsi que des exemples de manifestes Kubernetes.

---

## 1. Exemple de Dockerfile multi-stage durci

L'approche multi-stage permet de réduire drastiquement la taille de l'image finale et d'éliminer les outils de build de l'environnement de production.

```dockerfile title="Dockerfile optimisé et sécurisé"
# Étape 1 : Build de l'application
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o server .

# Étape 2 : Image d'exécution minimale
FROM alpine:3.19
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
WORKDIR /app
COPY --from=builder --chown=appuser:appgroup /app/server .
USER appuser
EXPOSE 8080
ENTRYPOINT ["./server"]
```

---

## 2. Déploiement Kubernetes avec Healthchecks

```yaml title="k8s-deployment.yaml"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-webapp
  labels:
    app: demo-webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: demo-webapp
  template:
    metadata:
      labels:
        app: demo-webapp
    spec:
      containers:
      - name: webapp
        image: demo-webapp:v1.0.0
        ports:
        - containerPort: 8080
        resources:
          limits:
            cpu: "500m"
            memory: "256Mi"
          requests:
            cpu: "100m"
            memory: "128Mi"
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
```
