# Server Monitoring Stack

<p align="center">
  <img src="https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/images/prometheus-logo.png" alt="Monitoring Stack Logo" width="180">
</p>

![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge&logo=open-source-initiative&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=Prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)

A comprehensive, containerized monitoring solution for quickly deploying a complete server monitoring environment using industry-standard tools. This stack provides real-time visibility into your infrastructure with minimal setup time.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Components](#components)
- [Quick Start](#quick-start)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Accessing Interfaces](#accessing-interfaces)
- [Configuration](#configuration)
  - [Adding Servers to Monitor](#adding-servers-to-monitor)
  - [Setting Up Alerts](#setting-up-alerts)
  - [Environment Variables](#environment-variables)
  - [Custom Exporters](#custom-exporters)
- [Dashboard Templates](#dashboard-templates)
- [Security Considerations](#security-considerations)
- [Maintenance](#maintenance)
  - [Backup and Restore](#backup-and-restore)
  - [Updates](#updates)
- [Troubleshooting](#troubleshooting)
  - [Common Issues](#common-issues)
  - [Logs](#logs)
- [Performance Tuning](#performance-tuning)
- [Contributing](#contributing)
- [License](#license)

## Overview

This monitoring stack provides a comprehensive solution for monitoring server performance, application health, and system metrics. It's designed to be easy to deploy and configure, making it perfect for both development environments and production systems.

The stack leverages Docker containers to provide a consistent, reproducible environment that can be deployed on any infrastructure that supports Docker. All components are preconfigured to work together out of the box, minimizing setup time and complexity.

## Features

- **Real-time metrics collection** - Gather CPU, memory, disk, and network statistics from hosts and containers
- **Customizable dashboards** - Create visual representations of your system's performance with Grafana
- **Alerting system** - Get notified when metrics exceed defined thresholds via email, Slack, or other channels
- **Long-term storage** - Retain historical metrics for trend analysis and capacity planning
- **Container monitoring** - Track container resource usage and health metrics
- **Low resource footprint** - Optimized for minimal impact on monitored systems
- **API endpoints** - Integrate with other tools and services through Prometheus API
- **Extensible architecture** - Add custom exporters for additional metrics collection

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Docker Network                           │
│                                                                 │
│  ┌─────────────┐       ┌───────────────┐      ┌──────────────┐  │
│  │             │       │               │      │              │  │
│  │  Prometheus │◄──────┤ Node Exporter │      │   Grafana    │  │
│  │  (Metrics   │       │ (Host Metrics)│      │ (Dashboards) │  │
│  │   Storage)  │       │               │      │              │  │
│  │             │       └───────────────┘      │              │  │
│  │             │                              │              │  │
│  │             │       ┌───────────────┐      │              │  │
│  │             │◄──────┤    cAdvisor   │      │              │  │
│  │             │       │  (Container   │      │              │  │
│  │             │       │   Metrics)    │      │              │  │
│  │             │       └───────────────┘      │              │  │
│  │             │◄─────────────────────────────┤              │  │
│  │             │            Query             │              │  │
│  └─────────────┘                              └──────────────┘  │
│    Port 9090                                     Port 3001      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

This architecture provides:

1. **Centralized metrics collection** - Prometheus scrapes metrics from all exporters
2. **Separation of concerns** - Each component has a single, well-defined responsibility
3. **Scalability** - Add more exporters without changing the core architecture
4. **Resilience** - Components can be restarted independently without data loss

## Components

The monitoring stack consists of the following components:

### Prometheus
Time series database for metrics storage and retrieval. Prometheus acts as the central component, collecting and storing metrics from various sources (exporters). It provides a powerful query language (PromQL) for data analysis.

### Grafana
Visualization and dashboarding tool that connects to Prometheus to display metrics in customizable dashboards. Grafana provides advanced visualization options, alerting capabilities, and user management.

### Node Exporter
System metrics collection agent that exports hardware and OS metrics from the host system. Node Exporter provides detailed information about CPU, memory, disk, network, and other system resources.

### cAdvisor
Container metrics collection agent that exports resource usage and performance data from running containers. cAdvisor provides visibility into container CPU, memory, network, and disk usage.

## Quick Start

### Prerequisites

To deploy the monitoring stack, you'll need:

- Docker Engine (version 20.10.0 or later)
- Docker Compose (version 2.0.0 or later)
- At least 1GB of RAM available for the stack
- Ports 3001 (Grafana), 9090 (Prometheus), and 8080 (cAdvisor) available

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/monitoring-stack.git
   cd monitoring-stack
   ```

2. (Optional) Configure environment variables:
   ```bash
   # Copy the example environment file
   cp .env.example .env
   
   # Edit the environment variables as needed
   nano .env
   ```

3. Start the monitoring stack:
   ```bash
   docker-compose up -d
   ```

4. Verify that all services are running:
   ```bash
   docker-compose ps
   ```

   You should see output similar to:
   ```
   NAME                COMMAND                  SERVICE             STATUS              PORTS
   cadvisor            "/usr/bin/cadvisor -…"   cadvisor            running             0.0.0.0:8080->8080/tcp
   grafana             "/run.sh"                grafana             running             0.0.0.0:3001->3000/tcp
   node-exporter       "/bin/node_exporter"     node-exporter       running             0.0.0.0:9100->9100/tcp
   prometheus          "/bin/prometheus --c…"   prometheus          running             0.0.0.0:9090->9090/tcp
   ```

### Accessing Interfaces

After deployment, you can access the following interfaces:

- **Grafana**: http://localhost:3001 (default credentials: admin/yourpassword)
- **Prometheus**: http://localhost:9090
- **cAdvisor**: http://localhost:8080

## Configuration

### Adding Servers to Monitor

To monitor additional servers:

1. Install Node Exporter on each target server:
   ```bash
   docker run -d --restart=unless-stopped --name=node-exporter \
     -p 9100:9100 \
     -v /proc:/host/proc:ro \
     -v /sys:/host/sys:ro \
     -v /:/rootfs:ro \
     --net="host" \
     prom/node-exporter:latest \
     --path.procfs=/host/proc \
     --path.sysfs=/host/sys \
     --collector.filesystem.mount-points-exclude="^/(sys|proc|dev|host|etc)($$|/)"
   ```

2. Add the server to `prometheus/prometheus.yml`:
   ```yaml
   - job_name: 'remote-node'
     static_configs:
       - targets: ['your-server-ip:9100']
         labels:
           instance: 'server-name'
   ```

3. Reload Prometheus configuration:
   ```bash
   curl -X POST http://localhost:9090/-/reload
   ```

### Setting Up Alerts

To configure alerting:

1. Create or edit alert rules in `prometheus/alert.rules.yml`:
   ```yaml
   groups:
   - name: example
     rules:
     - alert: HighCPULoad
       expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
       for: 5m
       labels:
         severity: warning
       annotations:
         summary: "High CPU load (instance {{ $labels.instance }})"
         description: "CPU load is > 80%\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
   ```

2. Update your Prometheus configuration to include the alert rules:
   ```yaml
   # In prometheus.yml
   rule_files:
     - 'alert.rules.yml'
   ```

3. Configure AlertManager in `alertmanager/config.yml`:
   ```yaml
   route:
     group_by: ['alertname']
     group_wait: 30s
     group_interval: 5m
     repeat_interval: 1h
     receiver: 'email-notifications'
   
   receivers:
   - name: 'email-notifications'
     email_configs:
     - to: 'your-email@example.com'
       from: 'alertmanager@example.com'
       smarthost: 'smtp.example.com:587'
       auth_username: 'smtp-user'
       auth_password: 'smtp-password'
   ```

4. Add AlertManager to your docker-compose.yml and restart the stack:
   ```yaml
   alertmanager:
     image: prom/alertmanager:latest
     container_name: alertmanager
     restart: unless-stopped
     ports:
       - "9093:9093"
     volumes:
       - ./alertmanager:/etc/alertmanager
     command:
       - '--config.file=/etc/alertmanager/config.yml'
       - '--storage.path=/alertmanager'
     networks:
       - monitoring-network
   ```

### Environment Variables

The stack uses environment variables for configuration:

- `GF_ADMIN_USER`: Grafana admin username (default: admin)
- `GF_ADMIN_PASSWORD`: Grafana admin password (default: yourpassword)

Additional Grafana configuration options can be added as environment variables with the `GF_` prefix.

### Custom Exporters

To monitor specific applications or services, add their respective exporters to the `docker-compose.yml` file:

```yaml
mysql-exporter:
  image: prom/mysqld-exporter:latest
  container_name: mysql-exporter
  restart: unless-stopped
  ports:
    - "9104:9104"
  environment:
    - DATA_SOURCE_NAME=user:password@(mysql:3306)/
  networks:
    - monitoring-network
```

Then add the exporter to your Prometheus configuration:

```yaml
- job_name: 'mysql'
  static_configs:
    - targets: ['mysql-exporter:9104']
```

## Dashboard Templates

The stack comes with pre-configured dashboards for:

- **System Overview**: CPU, memory
