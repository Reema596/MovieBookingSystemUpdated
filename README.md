# Movie Booking Application

This repository now includes:

- Spring Boot actuator health endpoints
- Prometheus metrics export
- Docker containerization
- Kubernetes manifests for app, MySQL, Prometheus, and Grafana
- GitHub Actions CI/CD pipeline

## Sequence to complete the project

1. Push this project to GitHub on the `main` branch.
2. Let GitHub Actions run the workflow in `.github/workflows/ci-cd.yml`.
3. Create the Kubernetes cluster.
5. Apply the manifests in this order:
   1. `kubectl apply -f k8s/namespace.yaml`
   2. `kubectl apply -f k8s/mysql-secret.yaml`
   3. `kubectl apply -f k8s/mysql-deployment.yaml`
   4. `kubectl apply -f k8s/app-secret.yaml`
   5. `kubectl apply -f k8s/app-deployment.yaml`
   6. `kubectl apply -f k8s/prometheus-config.yaml`
   7. `kubectl apply -f k8s/prometheus-deployment.yaml`
   8. `kubectl apply -f k8s/grafana-datasource.yaml`
   9. `kubectl apply -f k8s/grafana-deployment.yaml`
6. Verify the pods:
   `kubectl get pods -n movie-booking`
7. Verify monitoring:
   - Prometheus opens on port `9090`
   - Grafana opens on port `3000`
   - Application metrics are available at `/actuator/prometheus`
8. Take screenshots after deployment:
   - GitHub Actions pipeline success
   - `kubectl get pods -n movie-booking`
   - Prometheus target page showing the app as `UP`
   - Grafana dashboard or datasource health

## Local run

Use Maven:

```bash
mvn spring-boot:run
```

Use Docker:

```bash
docker build -t movie-booking-application .
docker run -p 8081:8081 movie-booking-application
```

## Monitoring endpoints

- Health: `http://localhost:8081/actuator/health`
- Prometheus metrics: `http://localhost:8081/actuator/prometheus`

## AWS CloudShell Deployment

Use AWS CloudShell from the AWS console. The commands below assume your repository is already on GitHub and your package image exists in GHCR.

1. Open AWS Console.
2. Select region `ap-south-1`.
3. Open CloudShell.
4. Verify tools and identity:

```bash
aws --version
kubectl version --client
eksctl version
aws sts get-caller-identity
```

5. Clone the repository:

```bash
git clone https://github.com/Reema596/MovieBookingSystemUpdated.git
cd MovieBookingSystemUpdated
```

6. Create the EKS cluster from the repo config:

```bash
eksctl create cluster -f eks/cluster-config.yaml
```

7. Refresh kubeconfig and verify the nodes:

```bash
aws eks update-kubeconfig --region ap-south-1 --name movie-booking-cluster
kubectl get nodes
```

8. Deploy the application and monitoring stack:

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/mysql-secret.yaml
kubectl apply -f k8s/mysql-deployment.yaml
kubectl apply -f k8s/app-secret.yaml
kubectl apply -f k8s/app-deployment.yaml
kubectl apply -f k8s/prometheus-config.yaml
kubectl apply -f k8s/prometheus-deployment.yaml
kubectl apply -f k8s/grafana-datasource.yaml
kubectl apply -f k8s/grafana-deployment.yaml
```

9. Verify:

```bash
kubectl get pods -n movie-booking
kubectl get svc -n movie-booking
kubectl get deployments -n movie-booking
```

10. Wait for the `EXTERNAL-IP` or AWS load balancer DNS names for:
   - `movie-booking-app`
   - `prometheus`
   - `grafana`

11. Open those URLs in a browser and take screenshots of:
   - GitHub Actions success
   - `kubectl get pods -n movie-booking`
   - `kubectl get svc -n movie-booking`
   - Prometheus target page showing the app `UP`
   - Grafana datasource or dashboard page

12. Delete the cluster after submission to stop AWS charges:

```bash
eksctl delete cluster -f eks/cluster-config.yaml
```

## Important manual updates

- Change all default passwords in the Kubernetes manifests before production use.
- The application image in `k8s/app-deployment.yaml` is set to `ghcr.io/Reema596/movie-booking-application:latest`.
- EKS costs money while the cluster and load balancers are running.
