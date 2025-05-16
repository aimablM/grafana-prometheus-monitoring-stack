<p align="center">
  <img src="img/amlogo.svg" alt="My Logo" width="100" height="80">
</p>

# Docker Based Grafana Prometheus Server Monitoring Stack

![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge&logo=open-source-initiative&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=Prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)

A comprehensive, containerized monitoring solution for quickly deploying a complete server monitoring environment using industry-standard tools. This stack provides real-time visibility into your infrastructure with minimal setup time.

## Visual Overview

Here's what you can expect from this monitoring stack:

### Main Components

<table>
  <tr>
    <td width="50%"><img src="docs/images/grafana-login.png" alt="Grafana Login" width="100%"/><br><em>Grafana Login Interface</em></td>
    <td width="50%"><img src="docs/images/docker-desktop.png" alt="Docker Desktop" width="100%"/><br><em>Docker Desktop with Running Containers</em></td>
  </tr>
  <tr>
    <td width="50%"><img src="docs/images/prometheus-config.png" alt="Prometheus Configuration" width="100%"/><br><em>Prometheus Data Source Configuration</em></td>
    <td width="50%"><img src="docs/images/grafana-home.png" alt="Grafana Home" width="100%"/><br><em>Grafana Home Dashboard</em></td>
  </tr>
</table>

### Dashboard Examples

<table>
  <tr>
    <td><img src="docs/images/container-dashboard-1.png" alt="Container Dashboard 1" width="100%"/><br><em>Docker Container & Host Metrics Overview</em></td>
  </tr>
  <tr>
    <td><img src="docs/images/container-dashboard-2.png" alt="Container Dashboard 2" width="100%"/><br><em>Detailed Container Metrics with Memory and CPU Usage</em></td>
  </tr>
  <tr>
    <td><img src="docs/images/node-exporter.png" alt="Node Exporter Dashboard" width="100%"/><br><em>Node Exporter Full Dashboard with System Metrics</em></td>
  </tr>
  <tr>
    <td><img src="docs/images/system-monitoring.png" alt="System Monitoring Dashboard" width="100%"/><br><em>Docker and System Monitoring Dashboard</em></td>
  </tr>
</table>

### Architecture

<p align="center">
  <img src="docs/images/architecture.png" alt="Architecture Diagram" width="80%"/>
  <br>
  <em>Deployment Architecture from Developer to Production</em>
</p>

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

<p align="center">
  <img src="docs/images/readme-example.png" alt="README Example" width="90%"/>
  <br>
  <em>The monitoring stack configuration shown in the code editor</em>
</p>

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

<p align="center">
  <img src="docs/images/stack-deployment.png" alt="Stack Deployment" width="90%"/>
  <br>
  <em>Monitoring stack deployment in action via Docker Compose</em>
</p>

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

<p align="center">
  <img src="docs/images/grafana-login.png" alt="Grafana Login" width="70%"/>
  <br>
  <em>Grafana Login Interface</em>
</p>

### Node Exporter
System metrics collection agent that exports hardware and OS metrics from the host system. Node Exporter provides detailed information about CPU, memory, disk, network, and other system resources.

<p align="center">
  <img src="docs/images/node-exporter.png" alt="Node Exporter Dashboard" width="90%"/>
  <br>
  <em>Node Exporter Full Dashboard with System Metrics</em>
</p>

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

<p align="center">
  <img src="docs/images/docker-desktop.png" alt="Docker Desktop" width="80%"/>
  <br>
  <em>Docker Desktop showing the running monitoring stack containers</em>
</p>

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

<p align="center">
  <img src="docs/images/prometheus-config.png" alt="Prometheus Configuration" width="80%"/>
  <br>
  <em>Prometheus Data Source Configuration in Grafana</em>
</p>

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

- **System Overview**: CPU, memory, disk, network metrics for hosts
- **Container Performance**: Resource usage metrics for Docker containers
- **Node Exporter Full**: Comprehensive host metrics dashboard
- **Prometheus Stats**: Monitoring of the monitoring system itself

<p align="center">
  <img src="docs/images/container-dashboard-1.png" alt="Container Dashboard" width="90%"/>
  <br>
  <em>Docker Container & Host Metrics Overview Dashboard</em>
</p>

<p align="center">
  <img src="docs/images/system-monitoring.png" alt="System Monitoring" width="90%"/>
  <br>
  <em>Docker and System Monitoring Dashboard</em>
</p>

### Importing Additional Dashboards

To import additional dashboards:

1. Access Grafana at http://localhost:3001
2. Go to "Dashboards" > "Import"
3. Enter the dashboard ID or upload the JSON file
4. Select the Prometheus data source
5. Click "Import"

Popular dashboard IDs:
- Node Exporter Full: 1860
- Docker & System Monitoring: 893
- Prometheus 2.0 Stats: 10000

<p align="center">
  <img src="docs/images/grafana-home.png" alt="Grafana Home" width="80%"/>
  <br>
  <em>Grafana Home Dashboard with Getting Started Guide</em>
</p>

## Security Considerations

### Authentication

- Change default credentials for all services
- Consider setting up OAuth or LDAP authentication for Grafana
- Implement API authentication for Prometheus

### Network Security

- Use a reverse proxy (like NGINX) for TLS termination
- Restrict access to management ports
- Configure firewalls to limit access to monitoring services

### Data Protection

- Implement regular backups of Prometheus and Grafana data
- Consider encrypting sensitive data in configuration files
- Scrub sensitive information from metrics and logs

### Example NGINX Configuration

```nginx
server {
    listen 80;
    server_name monitoring.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name monitoring.example.com;

    ssl_certificate /etc/letsencrypt/live/monitoring.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/monitoring.example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Maintenance

### Backup and Restore

To back up the monitoring stack:

```bash
# Stop the stack
docker-compose stop

# Back up data volumes
tar -czvf prometheus-data-backup.tar.gz /path/to/prometheus_data
tar -czvf grafana-data-backup.tar.gz /path/to/grafana_data

# Restart the stack
docker-compose start
```

To restore from backup:

```bash
# Stop the stack
docker-compose stop

# Restore data volumes
tar -xzvf prometheus-data-backup.tar.gz -C /path/to/restore
tar -xzvf grafana-data-backup.tar.gz -C /path/to/restore

# Update volume paths in docker-compose.yml if necessary
# Restart the stack
docker-compose start
```

### Updates

To update the monitoring stack components:

```bash
# Pull the latest images
docker-compose pull

# Restart with the new images
docker-compose up -d
```

For major version upgrades, check the release notes for each component to ensure compatibility.

## Troubleshooting

### Common Issues

#### Grafana Can't Connect to Prometheus

**Symptoms**: Grafana dashboards show "No data" or connection errors

**Solution**:
1. Check if Prometheus is running: `docker-compose ps prometheus`
2. Verify network connectivity: `docker exec -it grafana ping prometheus`
3. Check Grafana data source configuration:
   - URL should be `http://prometheus:9090`
   - Access should be set to `Server (default)`

#### High Disk Usage

**Symptoms**: Disk space fills up rapidly on the host system

**Solution**:
1. Check Prometheus storage usage: `docker exec -it prometheus du -sh /prometheus`
2. Adjust retention period in `prometheus.yml`:
   ```yaml
   storage:
     tsdb:
       retention.time: 15d
   ```
3. Consider implementing a downsampling strategy for older metrics

#### CPU/Memory Issues

**Symptoms**: High CPU or memory usage on the host system

**Solution**:
1. Reduce scrape frequency in `prometheus.yml`:
   ```yaml
   global:
     scrape_interval: 30s  # Default is 15s
   ```
2. Limit container resources in `docker-compose.yml`:
   ```yaml
   prometheus:
     # ...
     deploy:
       resources:
         limits:
           cpus: '0.5'
           memory: 1G
   ```

### Logs

View logs for troubleshooting:

```bash
# View logs for all services
docker-compose logs

# View logs for a specific service
docker-compose logs -f prometheus

# View logs with timestamps
docker-compose logs --timestamps
```

## Performance Tuning

### Prometheus Optimization

For larger deployments, consider the following optimizations:

1. Adjust scrape intervals based on metric importance:
   ```yaml
   scrape_configs:
     - job_name: 'critical-systems'
       scrape_interval: 10s
       # ...
     - job_name: 'non-critical-systems'
       scrape_interval: 60s
       # ...
   ```

2. Use the `sample_limit` parameter to prevent excessive cardinality:
   ```yaml
   global:
     scrape_config:
       sample_limit: 10000
   ```

3. Implement federation for large-scale deployments:
   ```yaml
   scrape_configs:
     - job_name: 'prometheus-federation'
       honor_labels: true
       metrics_path: '/federate'
       params:
         'match[]':
           - '{job="node"}'
       static_configs:
         - targets:
           - 'prometheus-secondary-1:9090'
           - 'prometheus-secondary-2:9090'
   ```

### Grafana Dashboard Efficiency

For better Grafana performance:

1. Limit the time range of dashboards (default view should be last 6-12 hours)
2. Use appropriate aggregation functions (rate, increase, avg_over_time)
3. Set reasonable refresh intervals (30s or more)
4. Consider breaking complex dashboards into multiple simpler dashboards

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch:
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. Commit your changes:
   ```bash
   git commit -m 'Add some amazing feature'
   ```
4. Push to the branch:
   ```bash
   git push origin feature/amazing-feature
   ```
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.