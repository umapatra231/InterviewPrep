Here’s a curated list of Kubernetes interview questions tailored for Java developers with 8+ years of experience, focusing on practical scenarios, integration with Java ecosystems, and advanced Kubernetes concepts:

1. Core Kubernetes Concepts
What is the difference between a Pod, Deployment, and StatefulSet?

Assesses understanding of workload management.

Explain how Kubernetes manages scaling for a Spring Boot microservice.

Horizontal Pod Autoscaler (HPA), resource requests/limits.

How do you configure environment variables for a Java app in Kubernetes?

ConfigMaps, Secrets, and envFrom in YAML.

What is a Service in Kubernetes? When would you use ClusterIP vs. NodePort?

2. Deploying Java Applications
Containerize a Spring Boot app: Write a Dockerfile with layered JAR optimizations.

Example:

dockerfile
Copy
FROM eclipse-temurin:17-jre  
COPY target/*.jar app.jar  
ENTRYPOINT ["java", "-jar", "/app.jar"]  
How do you configure JVM memory settings (Xmx/Xms) in a Kubernetes Pod?

Use JAVA_OPTS in the container’s args.

What is a livenessProbe and readinessProbe? Configure them for a Spring Boot Actuator health endpoint.

Example:

yaml
Copy
livenessProbe:  
  httpGet:  
    path: /actuator/health  
    port: 8080  
How would you handle database connection pooling (e.g., HikariCP) in a Kubernetes environment?

3. Configuration & Secrets
Inject Spring Boot application.properties from a ConfigMap.

Mount ConfigMap as a volume or use spring.config.import.

Securely manage database passwords for a Java app using Kubernetes Secrets.

Example:

yaml
Copy
env:  
  - name: DB_PASSWORD  
    valueFrom:  
      secretKeyRef:  
        name: db-secret  
        key: password  
How do you refresh configuration changes in a Java app without restarting the Pod?

Use Spring Cloud Kubernetes Config Watcher or sidecar reloaders.

4. Monitoring & Logging
Integrate Micrometer/Prometheus with Kubernetes for a Java app.

Expose /actuator/prometheus and configure Prometheus Operator.

Centralize logs from Java apps using the EFK stack (Elasticsearch, Fluentd, Kibana).

Use Fluentd DaemonSet to collect container logs.

How would you debug a Java app OOMKilled error in Kubernetes?

Analyze kubectl describe pod, JVM heap dumps, and Grafana dashboards.

5. Networking & Service Mesh
Configure Ingress for a Spring Boot app with path-based routing.

Example:

yaml
Copy
rules:  
  - http:  
      paths:  
        - path: /api/*  
          pathType: Prefix  
          backend:  
            service:  
              name: java-service  
              port: 8080  
Secure inter-service communication between Java microservices using mTLS (e.g., Istio).

What is a Headless Service, and when would you use it with a Java app?

For stateful applications like Kafka or Cassandra.

6. Stateful Applications
Deploy a Kafka cluster with Java consumers using StatefulSets.

Example: Use Strimzi Operator for Kafka on Kubernetes.*

How do you manage database schema migrations (e.g., Flyway) in Kubernetes?

Init containers or Kubernetes Jobs.

Configure persistent storage for a Java app using PersistentVolumeClaims.

7. CI/CD & GitOps
Design a CI/CD pipeline for a Java app using Jenkins and Kubernetes.

Stages: Build → Test → Containerize → Deploy (Helm/ArgoCD).

Implement GitOps for a Spring Boot app using ArgoCD.

Sync Kubernetes manifests from a Git repository.

How do you roll back a failed deployment of a Java microservice?

Use kubectl rollout undo deployment/<name>.

8. Security
Secure a Java app in Kubernetes using RBAC and NetworkPolicies.

Scan Java container images for vulnerabilities using Trivy or Clair.

How do you manage TLS certificates for a Spring Boot app (e.g., cert-manager)?

9. Advanced Topics
Autoscale a Java app based on custom metrics (e.g., JMS queue depth).

Use KEDA (Kubernetes Event-Driven Autoscaling).

Write a Kubernetes Operator in Java (e.g., using Fabric8 or Operator SDK).

Optimize JVM settings for Kubernetes (e.g., -XX:+UseContainerSupport).

10. Troubleshooting Scenarios
A Java app is slow. How would you diagnose it in Kubernetes?

Check CPU throttling, garbage collection logs, and network latency.

Debug a ConnectionRefused error between two Java microservices.

Check Services, DNS resolution, and NetworkPolicies.

A Pod is stuck in CrashLoopBackOff. How do you investigate?

Use kubectl logs --previous and kubectl describe pod.

Key Areas for Senior Developers
Trade-offs: Stateful vs. stateless deployments, monolith vs. microservices.

Cost Optimization: Right-sizing resource requests/limits.

Disaster Recovery: Backup/Restore (Velero), multi-region deployments.

Observability: Distributed tracing (e.g., Jaeger + OpenTelemetry).
