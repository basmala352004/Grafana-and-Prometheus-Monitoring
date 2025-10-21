# 📘 Monitoring & Logging with Prometheus + Grafana

> 🚀 Real-time dashboards for application health, performance, and resource usage, in just minutes!

---

## 🧩 What’s Inside?

This setup helps you monitor any web application (Java, Node.js, Python, Go, etc.) using:

| Tool        | Purpose                              |
|-------------|--------------------------------------|
| 🧠 Prometheus | Collects & stores app metrics         |
| 📊 Grafana    | Visualizes metrics on dashboards     |
| 🐳 Docker     | Simplifies setup with containers     |

---

## 📦 Prerequisites

Make sure you have:

- 🐳 **Docker + Docker Compose**
- 💻 **Your Application** (with a `/metrics` endpoint)
- 🌐 **Internet Connection** (to pull images)

---

## 🏗️ Folder Structure

```bash
monitoring/
├── docker-compose.yml       # Runs Prometheus + Grafana
└── prometheus.yml           # Prometheus config
```

---

## ⚙️ Prometheus Configuration

Create `prometheus.yml`:

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'application'
    metrics_path: '/metrics'
    static_configs:
      - targets: ['host.docker.internal:8080']
```

> 💡 If your app runs in Docker, use its service name instead of `host.docker.internal`.

---

## 🐳 Docker Compose Setup

Create `docker-compose.yml`:

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - monitoring

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
    networks:
      - monitoring
    depends_on:
      - prometheus

networks:
  monitoring:
    driver: bridge
```

---

## ▶️ Run the Setup

```bash
docker-compose up -d
```

- Prometheus → [http://localhost:9090](http://localhost:9090)
- Grafana → [http://localhost:3000](http://localhost:3000)

---

## 📊 Grafana Configuration

1. Login: `admin / admin`
2. Add Data Source → Prometheus → URL: `http://prometheus:9090`
3. Import Dashboard → Use ID `4701` or build your own!

---

## 🔍 Verify Prometheus Targets

Visit [http://localhost:9090/targets](http://localhost:9090/targets)

✅ Green = UP  
❌ Red = DOWN → Check your app, endpoint, or port

---

## 📈 Common Metrics

| Metric Type     | Description                     | Example Name                    |
|-----------------|---------------------------------|----------------------------------|
| CPU Usage       | CPU consumed by app             | `process_cpu_seconds_total`     |
| Memory Usage    | RAM used                        | `process_resident_memory_bytes` |
| HTTP Requests   | Total requests received         | `http_requests_total`           |
| Request Duration| Avg. response time              | `http_request_duration_seconds` |
| Errors          | Failed requests                 | `http_request_errors_total`     |
| GC Activity     | JVM garbage collection          | `jvm_gc_pause_seconds_count`    |

---

## 🧹 Stop & Clean Up

```bash
docker-compose down
docker-compose down -v --remove-orphans
```

---

## 🏗️ Architecture Diagram

```
+-----------------------------+
| Your Application            |
| (exposes /metrics endpoint)|
+--------------+--------------+
               |
               v
+-------------------+
|   Prometheus      |
| Scrapes metrics   |
+--------+----------+
         |
         v
+-------------------+
|     Grafana       |
| Visualizes data   |
+-------------------+
```

---

## ✅ Final Result

- Prometheus scrapes metrics every 5s
- Grafana shows real-time dashboards
- You gain full visibility into app health & performance



