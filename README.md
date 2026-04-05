# Movie Booking Application

This repository now includes:

- Spring Boot actuator health endpoints
- Prometheus metrics export
- Docker containerization
- Kubernetes manifests for app, MySQL, Prometheus, and Grafana
- GitHub Actions CI/CD pipeline

## Sequence to complete the project

1. Create a new GitHub repository and push this project to the `main` branch.
2. Update the container image in `k8s/app-deployment.yaml` from `ghcr.io/replace-me/movie-booking-application:latest` to your real GitHub Container Registry image.
3. Let GitHub Actions run the workflow in `.github/workflows/ci-cd.yml`.
4. Build a Kubernetes cluster using Minikube, Docker Desktop Kubernetes, Kind, or a cloud cluster.
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

## Important manual updates

- Change all default passwords in the Kubernetes manifests before production use.
- Replace the placeholder image name in `k8s/app-deployment.yaml`.
- This workspace is not currently a Git repository, so GitHub push and live deployment still need to be done from your side or after `git init`.
