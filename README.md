# 📘 Monitoring & Logging with Prometheus + Grafana  
### 🔍 Dashboards for Application Health Metrics

---

## 🧩 Overview

This guide explains step-by-step how to:
- **Run Prometheus** (a monitoring system that collects metrics)
- **Run Grafana** (a visualization tool for dashboards)
- **Connect them to your application** (any technology)
- **View real-time metrics** such as CPU usage, memory, request rate, errors, etc.

This setup helps developers and testers track **app performance, stability, and resource consumption** during development or production.

---

## 🚀 1. Prerequisites

Before starting, ensure you have:

| Requirement | Description |
|--------------|-------------|
| 🐳 **Docker + Docker Compose** | To run Prometheus and Grafana easily |
| 💻 **Your Application** | Any web app (Java, Node.js, Python, Go, etc.) that can expose a metrics endpoint |
| 🌐 **Internet Connection** | Needed to pull images for Prometheus and Grafana |

---

## 🏗️ 2. Folder Structure

Create a directory for your monitoring setup:

monitoring/ │ ├── docker-compose.yml        # Defines the services (Prometheus + Grafana) └── prometheus.yml            # Configuration file for Prometheus

---

## ⚙️ 3. Configure Prometheus

Create a file called **`prometheus.yml`**  
This file tells Prometheus **what to monitor**, **how often**, and **where** to get metrics.

```yaml
global:
  scrape_interval: 5s    # Prometheus collects (scrapes) data every 5 seconds

scrape_configs:
  - job_name: 'application'          # Name shown in Prometheus target list
    metrics_path: '/metrics'         # Path where your app exposes metrics
    static_configs:
      - targets: ['host.docker.internal:8080']

🧠 Explanation:

Key	Meaning	Editable?

scrape_interval	How frequently Prometheus collects metrics	✅ Yes
job_name	Logical name of your app (used for grouping)	✅ Yes
metrics_path	The HTTP endpoint where your app exposes metrics	✅ Yes
targets	The host & port of your app	✅ Yes



---

💬 Example per technology:

Technology	Common Metrics Path	Example

Java (Micrometer / Spring Boot)	/actuator/prometheus	metrics_path: /actuator/prometheus
Node.js (prom-client)	/metrics	metrics_path: /metrics
Python (prometheus_client)	/metrics	same
Go (Prometheus library)	/metrics	same


> 💡 If your app runs inside Docker, replace host.docker.internal with the service name of your app from docker-compose (e.g., app:8080).




---

🐳 4. Create Docker Compose File

Create docker-compose.yml
This file defines and runs Prometheus and Grafana as containers.

version: '3.8'

services:
  prometheus:
    image: prom/prometheus                      # Official Prometheus image
    container_name: prometheus
    ports:
      - "9090:9090"                             # Access Prometheus via localhost:9090
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml  # Mount config file
    networks:
      - monitoring                              # Shared network for both services

  grafana:
    image: grafana/grafana                      # Official Grafana image
    container_name: grafana
    ports:
      - "3000:3000"                             # Access Grafana via localhost:3000
    networks:
      - monitoring
    depends_on:
      - prometheus                              # Start Prometheus first

networks:
  monitoring:
    driver: bridge

🧠 Explanation:

Part	Meaning	Editable?

image	The Docker image to use	❌ Usually keep as is
ports	Maps internal container ports to your host	✅ You can change ports if needed
volumes	Links your config file to container	⚠️ Keep as is unless you rename files
networks	Used for communication between Prometheus & Grafana	❌ Keep as is
depends_on	Ensures Grafana waits for Prometheus to start	❌ Keep as is



---

▶️ 5. Run the Setup

Run this command in the same directory:

docker-compose up -d

What Happens:

Prometheus starts at 👉 http://localhost:9090

Grafana starts at 👉 http://localhost:3000


To check containers:

docker ps

To stop them:

docker-compose down


---

📊 6. Configure Grafana

Once Grafana is running:

1. Open http://localhost:3000

Default login credentials:
Username: admin
Password: admin



2. Add a Data Source:

Go to Connections → Data Sources → Add new data source

Select Prometheus

In the URL field, write:

http://prometheus:9090

Click Save & Test



3. Create or Import Dashboard:

Go to Dashboards → New → Import

You can import a ready-made dashboard (e.g., ID 4701 for general metrics)

Or build your own with queries.





---

🔎 7. Test Prometheus Connection

Go to:

http://localhost:9090/targets

You should see your application listed with a green "UP" status.
If it shows "DOWN", check:

The metrics endpoint path

The target host/port

The app is running



---

📈 8. Common Metrics You Can Monitor

Metric Type	Description	Example Metric Name

CPU Usage	CPU consumption by process	process_cpu_seconds_total
Memory Usage	RAM used by app	process_resident_memory_bytes
HTTP Requests	Number of received requests	http_requests_total
Request Duration	Average response time	http_request_duration_seconds
Errors	Number of failed requests	http_request_errors_total
GC Activity	Garbage collection count/time (for JVM apps)	jvm_gc_pause_seconds_count



---

⚠️ 9. Troubleshooting Tips

Issue	Possible Cause	Solution

Prometheus shows “DOWN”	Wrong target or metrics path	Check prometheus.yml values
Grafana can’t connect to Prometheus	Wrong URL	Use http://prometheus:9090 inside Docker
No metrics visible	App doesn’t expose metrics	Add /metrics endpoint via Prometheus client
Port conflict	9090 or 3000 already in use	Change ports in docker-compose.yml



---

🧹 10. Stop and Clean Up

docker-compose down

If you also want to remove volumes and networks:

docker-compose down -v --remove-orphans


---

🏗️ 11. Architecture & Data Flow

Below is the logical architecture showing how the system works:

+-----------------------------+
          |       Your Application      |
          |  (any tech exposing /metrics)|
          +--------------+--------------+
                         |
                         |  (HTTP scrape)
                         v
               +-------------------+
               |    Prometheus     |
               | Collects metrics  |
               | from your app     |
               +--------+----------+
                        |
                        | (Data source)
                        v
               +-------------------+
               |     Grafana       |
               |  Visualizes data  |
               |  on dashboards    |
               +-------------------+

🔁 Data Flow Summary:

1. Your Application exposes metrics in plain text format at /metrics or similar endpoint.


2. Prometheus regularly scrapes these metrics and stores them in its internal time-series database.


3. Grafana connects to Prometheus as a data source, and visualizes the metrics using charts, graphs, and alerts.


4. The user (developer/tester) views all data in Grafana dashboards for monitoring app health and performance.




---

✅ Final Result

After setup:

Prometheus continuously scrapes your app’s metrics

Grafana visualizes these metrics on real-time dashboards

You get clear insights into application health, performance, and bottlenecks



---

🧠 Summary

Tool	Purpose

Prometheus	Collects & stores application metrics
Grafana	Displays metrics on dashboards
Docker Compose	Simplifies setup of both tools
Metrics Endpoint	Provides Prometheus-compatible metrics
