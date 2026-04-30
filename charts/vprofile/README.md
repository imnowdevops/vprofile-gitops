# vprofile-chart

Helm chart for deploying the vprofile application stack on Kubernetes (AWS EKS).

## Components

| Component | Image | Port |
|-----------|-------|------|
| App (Tomcat) | `vprocontainers/vprofileapp` | 8080 |
| Database (MySQL) | `vprocontainers/vprofiledb` | 3306 |
| Memcached | `memcached` | 11211 |
| RabbitMQ | `rabbitmq` | 5672 |

## Prerequisites

- Kubernetes 1.23+
- Helm 3.x
- AWS EKS with EBS CSI driver (for `gp2` storage class)
- AWS Load Balancer Controller (for ALB ingress)

## Install

```bash
helm install vprofile ./vprofile-chart
```

With a custom values file:

```bash
helm install vprofile ./vprofile-chart -f my-values.yaml
```

## Upgrade

```bash
helm upgrade vprofile ./vprofile-chart
```

## Uninstall

```bash
helm uninstall vprofile
```

## Values

### app

| Key | Default | Description |
|-----|---------|-------------|
| `app.image` | `vprocontainers/vprofileapp` | App container image |
| `app.tag` | `latest` | Image tag |
| `app.replicas` | `1` | Number of replicas |
| `app.containerPort` | `8080` | Container port |
| `app.servicePort` | `8080` | Service port |

### db

| Key | Default | Description |
|-----|---------|-------------|
| `db.image` | `vprocontainers/vprofiledb` | DB container image |
| `db.tag` | `latest` | Image tag |
| `db.replicas` | `1` | Number of replicas |
| `db.containerPort` | `3306` | Container port |
| `db.storageClass` | `gp2` | StorageClass for EBS PVC |
| `db.storageSize` | `3Gi` | PVC storage size |

### memcached

| Key | Default | Description |
|-----|---------|-------------|
| `memcached.image` | `memcached` | Memcached image |
| `memcached.tag` | `latest` | Image tag |
| `memcached.replicas` | `1` | Number of replicas |
| `memcached.containerPort` | `11211` | Container port |
| `memcached.servicePort` | `11211` | Service port |

### rabbitmq

| Key | Default | Description |
|-----|---------|-------------|
| `rabbitmq.image` | `rabbitmq` | RabbitMQ image |
| `rabbitmq.tag` | `latest` | Image tag |
| `rabbitmq.replicas` | `1` | Number of replicas |
| `rabbitmq.containerPort` | `5672` | Container port |
| `rabbitmq.defaultUser` | `guest` | Default RabbitMQ user |

### initcontainers

| Key | Default | Description |
|-----|---------|-------------|
| `initcontainers.image` | `busybox` | Init container image |
| `initcontainers.tag` | `latest` | Image tag |

### ingress

| Key | Default | Description |
|-----|---------|-------------|
| `ingress.enabled` | `true` | Enable AWS ALB ingress |
| `ingress.host` | `vprofile.hkhinfoteck.xyz` | Ingress hostname |
| `ingress.servicePort` | `8080` | Backend service port |

### secrets

| Key | Default | Description |
|-----|---------|-------------|
| `secrets.dbPass` | `dnByb2RicGFzcw==` | Base64-encoded MySQL root password |
| `secrets.rmqPass` | `Z3Vlc3Q=` | Base64-encoded RabbitMQ password |

### dockerregistry

| Key | Default | Description |
|-----|---------|-------------|
| `dockerregistry.enabled` | `false` | Create docker registry secret and add imagePullSecrets |
| `dockerregistry.server` | `https://index.docker.io/v1/` | Registry server URL |
| `dockerregistry.username` | `""` | Registry username |
| `dockerregistry.password` | `""` | Registry password |
| `dockerregistry.email` | `""` | Registry email |

## Private Registry

To pull images from a private registry, enable the docker registry secret:

```yaml
dockerregistry:
  enabled: true
  server: https://index.docker.io/v1/
  username: <your-username>
  password: <your-password>
  email: <your-email>
```

When enabled, `imagePullSecrets` is automatically added to the app and db deployments.

## Ingress

The ingress uses the AWS Load Balancer Controller with the following annotations:

```yaml
kubernetes.io/ingress.class: alb
alb.ingress.kubernetes.io/scheme: internet-facing
alb.ingress.kubernetes.io/target-type: ip
```

To disable ingress:

```yaml
ingress:
  enabled: false
```

## Secrets

`secrets.dbPass` and `secrets.rmqPass` must be **base64-encoded** values.

To encode a new password:

```bash
echo -n "yourpassword" | base64
```

## Template Files

| File | Resource |
|------|----------|
| `app-deployment.yaml` | App Deployment |
| `db-deployment.yaml` | DB Deployment + PVC mount |
| `mc-deployment.yaml` | Memcached Deployment |
| `rmq-deployment.yaml` | RabbitMQ Deployment |
| `services.yaml` | All 4 ClusterIP Services |
| `ingress.yaml` | ALB Ingress (conditional) |
| `secret.yaml` | App Secret (db-pass, rmq-pass) |
| `pvc.yaml` | DB PersistentVolumeClaim |
| `dockerregistry-secret.yaml` | Docker Registry Secret (conditional) |
