# Architecture

## Main monitoring path

```text
Linux host
  |
  +-- node_exporter :9100 ------------------+
  |                                          |
  +-- Prometheus self metrics :9090 --------+--> Prometheus --> Grafana
                                             |
/proc + df                                   |
  |                                          |
  +--> Bash exporter --> metrics file --> Nginx :80 /metrics
                                             |
                                             +--> Prometheus
```

## Components

### node_exporter

Provides standard Linux host metrics over HTTP on port `9100`.

### Prometheus

Scrapes metrics using the pull model and stores them as time series. The project used:

- `localhost:9090` for Prometheus self-monitoring;
- `localhost:9100` for node_exporter;
- `localhost:80/metrics` for the custom Bash exporter.

### Custom Bash exporter

The exporter reads data from:

- `/proc/stat` - CPU counters;
- `/proc/meminfo` - RAM and Swap;
- `/proc/diskstats` - disk I/O counters;
- `/proc/uptime` - system uptime;
- `/proc/loadavg` - load average;
- `df -B1 /` - root filesystem capacity and usage.

Metrics are first written to a temporary file and then atomically moved to the final metrics file. This prevents Nginx/Prometheus from reading a partially written file.

### Nginx

Serves the generated metrics file through an HTTP endpoint:

```nginx
location = /metrics {
    default_type text/plain;
    alias /var/www/html/metrics;
}
```

### Grafana

Uses Prometheus as the data source and visualizes system metrics on a custom dashboard.

Grafana was run in Docker with host networking and a persistent volume.
