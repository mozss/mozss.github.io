---
title: kubernetes的实践
tags:
  - kubernetes
categories:
  - code
  - code-golang
abbrlink: 62876
date: 2022-11-21 01:16:29
---
[![ppnXRUS.png](https://s1.ax1x.com/2023/03/10/ppnXRUS.png)](https://imgse.com/i/ppnXRUS)

<!--more-->

# Kubernetes落地实践

## 简介

Kubernetes 是一款开源的容器编排和管理工具，可以帮助我们更高效地管理云原生环境中的容器应用。在本文中，我们将介绍如何将 Kubernetes 应用于真实的生产环境中，并展示具体的实践经验。

## 准备工作

在开始使用 Kubernetes 之前，需要先完成以下准备工作：

1. 部署 Kubernetes 集群：可以使用 Kubespray、Kops、Minikube 等工具来快速部署 Kubernetes 集群；
2. 部署容器镜像仓库：可以使用 Docker Registry 或者 Harbor 等容器镜像仓库来存储和管理镜像；
3. 部署监控和日志工具：可以使用 Prometheus、Grafana、ELK 等工具来监控容器的状态和收集日志。

## 实践案例

### 部署 Spring Boot 应用

假设我们已经有一个简单的 Spring Boot 应用，需要将它部署到 Kubernetes 集群中。以下是具体的步骤：

1. 将应用打包成容器镜像，并上传到镜像仓库中：

```shell
# 编写Dockerfile
FROM openjdk:8-jdk-alpine
COPY target/demo-0.0.1-SNAPSHOT.jar /app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
# 打包镜像
docker build -t <image_name>:<tag> .
# 上传镜像
docker push <image_name>:<tag>
```

2. 编写 Kubernetes 配置文件（deployment.yml）：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: demo
        image: <image_name>:<tag>
        ports:
        - containerPort: 8080
```

3. 应用 Kubernetes 配置文件：

```shell
kubectl apply -f deployment.yml
```

### 部署分布式应用

假设我们需要部署一个分布式应用，包含多个微服务，这些微服务需要相互调用。以下是具体的步骤：

1. 使用 Kubernetes 部署 Redis，用于服务之间的数据共享：

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
spec:
  serviceName: redis
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:alpine
        ports:
        - containerPort: 6379
        volumeMounts:
        - name: data
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

2. 部署服务网关（使用 Istio）：

```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: demo
spec:
  hosts:
  - "*"
  gateways:
  - gateway
  http:
  - route:
    - destination:
        host: api-service
        port:
          number: 8080
      weight: 100
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: api-service
spec:
  host: api-service
  trafficPolicy:
    loadBalancer:
      consistentHash:
        httpHeaderName: "x-correlation-id"
        httpCookie:
          name: "X-Correlation-ID"
```

3. 部署微服务之间的通信：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: <image_name>:<tag>
        ports:
        - containerPort: 8080
        env:
        - name: REDIS_HOST
          value: redis.default.svc.cluster.local
        - name: REDIS_PORT
          value: "6379"
        - name: REDIS_PASSWORD
          valueFrom:
            secretKeyRef:
              name: redis-secret
              key: password
---
apiVersion: v1
kind: Service
metadata:
  name: api-service
  labels:
    app: api
spec:
  selector:
    app: api
  type: ClusterIP
  ports:
  - name: http
    port: 8080
    targetPort: 8080
```

在上述配置中，我们使用了 Istio 进行服务网关和流量控制，并使用 Redis 存储微服务之间的数据。

## 总结

在实际的生产环境中，Kubernetes 可以帮助我们更高效地管理容器应用，并提供了丰富的功能和插件，例如服务发现、负载均衡、故障恢复、流量控制等。在使用 Kubernetes 之前，需要做好充分的准备工作，并根据实际的情况进行设计和优化。