## 🧠 Core Engineering Concepts to Master

-  **OpenTelemetry (OTel):** Standardizing how telemetry data is generated and collected.
-  **Secure Coding Principles:** Applying threat modeling frameworks like STRIDE, and implementing data protection and PII masking.
-  **Access Management Protocols:** Understanding identity and access control, specifically cloud RBAC (Role-Based Access Control) and IRSA (IAM Roles for Service Accounts).
-  **Kubernetes (K8s):** Writing YAML manifests to dictate how Kubernetes runs, scales, and routes traffic to specific microservices.
-  **Observability:** Exposing metrics from application code, writing queries to monitor application health, and building visual dashboards.
-  **CI/CD:** Writing pipeline configurations to automatically build, test, and deploy specific code repositories.

## 🚢 Module 1: Kubernetes for SDEs

> **Focus:** Master the "Application Primitives" — the core YAML objects that represent your deployed application.

-  **Pods & Deployments:** Learn how to run your Docker container and ensure a specific number of replicas are always running and maintained.
-  **Services:** Understand how to expose your application over the network so other microservices can communicate with it.
-  **ConfigMaps & Secrets:** Practice passing environment variables, database URLs, and passwords into your container dynamically without hardcoding them in your source code.
-  **Resource Requests & Limits:** Learn how to tell K8s exactly how much CPU and RAM your application needs to function properly, and set bounds for when it should be killed for consuming too much.
-  **Probes (Liveness & Readiness):** Write dedicated endpoints in your app (like `/health`) so K8s knows if your app is ready to receive traffic or if it has hung and needs to be restarted.  

## 📊 Module 2: Observability (Prometheus, Grafana, OTel)

> **Focus:** Understand exactly how your application behaves in a live production environment.

-  **The RED Method:** Master the three golden signals for backend services: **Rate** (requests per second), **Errors** (HTTP 5xx rate), and **Duration** (latency/response time).
-  **Instrumenting Code:** Use a Prometheus client library (in Go, Python, or Java) to expose a `/metrics` endpoint directly within your application code.
-  **Basic PromQL:** Learn enough Prometheus Query Language (PromQL) to calculate critical operational metrics, such as the error rate or the 99th percentile (p99) latency of your service.
-  **Dashboards:** Create a basic Grafana dashboard that visualizes those PromQL queries for real-time monitoring.

## ⚙️ Module 3: CI/CD

> **Focus:** Automating the journey of getting your code from your laptop to a deployable, production-ready state.

-  **Basic Pipelines:** Write a simple GitHub Actions (`.yml`) workflow that triggers on a pull request to automatically run your unit tests, build your Docker image, and push it to a container registry.