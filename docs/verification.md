# Verification and troubleshooting

The project was checked layer by layer: service -> listening port -> HTTP endpoint -> Prometheus target/query -> Grafana panel.

## 1. Check services

```bash
systemctl status prometheus --no-pager
systemctl status prometheus-node-exporter --no-pager
```

`--no-pager` prints the result directly instead of opening it in an interactive pager.

## 2. Check node_exporter output

```bash
curl http://localhost:9100/metrics | head
```

- `curl` performs an HTTP request to the metrics endpoint;
- `head` limits the output to the first 10 lines.

## 3. Check listening TCP ports

```bash
sudo ss -lntp | grep -E ':9090|:9100'
```

`ss` options:

- `-l` - show listening sockets;
- `-n` - show numeric addresses/ports without name resolution;
- `-t` - show TCP sockets;
- `-p` - show the process using each socket.

`grep -E` enables extended regular expressions so one expression can match both ports.

Expected ports:

- `9090` - Prometheus;
- `9100` - node_exporter.

## 4. Validate Prometheus configuration

```bash
sudo promtool check config /etc/prometheus/prometheus.yml
```

This catches YAML/configuration errors before restarting Prometheus.

## 5. Check Prometheus readiness

```bash
curl http://localhost:9090/-/ready
```

A successful response confirms that Prometheus is ready to serve requests.

## 6. Check the custom exporter endpoint

```bash
curl http://localhost/metrics
curl -I http://localhost/metrics
```

For `curl`, `-I` requests response headers only. The endpoint should return an HTTP `200` response and `Content-Type: text/plain`.

## 7. Check the custom Prometheus target

PromQL:

```promql
up{job="custom_node_exporter"}
```

Expected value:

```text
1
```

`1` means Prometheus can successfully scrape the target.

## 8. Check selected custom metrics

```promql
custom_cpu_usage_percent
```

```promql
custom_memory_available_bytes
```

```promql
custom_disk_free_bytes{mountpoint="/"}
```

If these work in Prometheus but a Grafana panel is empty, the next checks should focus on the Grafana data source, panel query, time range and units rather than the exporter itself.
