# Server Monitoring Stack

A lightweight, containerized monitoring solution for quickly deploying a complete server monitoring environment.

## Overview

This monitoring stack provides a comprehensive solution for monitoring server performance, application health, and system metrics. It's designed to be easy to deploy and configure, making it perfect for both development environments and production systems.

## Features

- **Real-time metrics collection** - Gather CPU, memory, disk, and network statistics
- **Customizable dashboards** - Create visual representations of your system's performance
- **Alerting system** - Get notified when metrics exceed defined thresholds
- **Log aggregation** - Centralize logs from multiple servers for easier troubleshooting
- **Container monitoring** - Track container resource usage and health
- **API endpoints** - Integrate with other tools and services

## Components

The monitoring stack consists of the following components:

1. **Prometheus** - Time series database for metrics storage
2. **Grafana** - Visualization and dashboarding
3. **Node Exporter** - System metrics collection
4. **cAdvisor** - Container metrics collection

## Quick Start

### Prerequisites

- Docker and Docker Compose installed on your system
- Ports 3000 (Grafana), 9090 (Prometheus), and 9093 (AlertManager) available

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/monitoring-stack.git
   cd monitoring-stack
   ```

2. Start the monitoring stack:
   ```bash
   docker-compose up -d
   ```

3. Access the interfaces:
   - Grafana: http://localhost:3000 (default credentials: admin/admin)
   - Prometheus: http://localhost:9090
   - AlertManager: http://localhost:9093

## Configuration

### Adding Servers to Monitor

1. Install Node Exporter on each target server:
   ```bash
   docker run -d --name node-exporter -p 9100:9100 prom/node-exporter
   ```

2. Add the server to `prometheus/prometheus.yml`:
   ```yaml
   - job_name: 'node'
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

1. Edit `alertmanager/alertmanager.yml` to configure notification channels (email, Slack, etc.)
2. Define alert rules in `prometheus/alert.rules.yml`
3. Restart AlertManager to apply changes:
   ```bash
   docker-compose restart alertmanager
   ```

## Dashboard Templates

The stack comes with pre-configured dashboards for:

- System overview (CPU, memory, disk, network)
- Container performance
- Application metrics
- Alert status

Import additional dashboards from the [Grafana dashboard repository](https://grafana.com/grafana/dashboards/).

## Extending the Stack

### Adding Custom Exporters

To monitor specific applications or services, add their respective exporters to the `docker-compose.yml` file:

```yaml
mysql-exporter:
  image: prom/mysqld-exporter
  ports:
    - "9104:9104"
  environment:
    - DATA_SOURCE_NAME=user:password@(mysql:3306)/
```

### Custom Metrics

To add custom metrics:

1. Create a new exporter or use the Prometheus client library
2. Expose metrics on an HTTP endpoint
3. Add the endpoint to Prometheus configuration

## Troubleshooting

### Common Issues

- **Can't access Grafana**: Check if the container is running with `docker ps` and verify port 3000 is not being used by another application.
- **No metrics showing**: Ensure Node Exporter is properly installed and accessible from the Prometheus server.
- **Alerts not firing**: Check AlertManager configuration and connectivity.

### Logs

View container logs for troubleshooting:

```bash
docker-compose logs -f [service_name]
```

## Maintenance

### Backup

Backup Prometheus data and Grafana configurations:

```bash
docker-compose stop
tar -czvf monitoring-backup.tar.gz prometheus/data grafana/data
docker-compose start
```

### Updates

Update the stack components:

```bash
docker-compose pull
docker-compose up -d
```

## Security Considerations

- Change default credentials for all services
- Use HTTPS for production environments
- Restrict access to management ports
- Consider implementing authentication for exporters

## License

[MIT License](LICENSE)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
