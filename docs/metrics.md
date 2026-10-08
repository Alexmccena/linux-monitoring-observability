# Custom metrics

The custom exporter emits Prometheus text-format metrics.

## CPU

Source: `/proc/stat`

| Metric | Type | Meaning |
|---|---|---|
| `custom_cpu_usage_percent` | gauge | Total CPU usage percentage |
| `custom_cpu_user_percent` | gauge | User-space CPU percentage |
| `custom_cpu_system_percent` | gauge | Kernel/system CPU percentage |
| `custom_cpu_idle_percent` | gauge | Idle CPU percentage |
| `custom_cpu_iowait_percent` | gauge | CPU time waiting for I/O |

## Memory

Source: `/proc/meminfo`

| Metric | Type | Meaning |
|---|---|---|
| `custom_memory_total_bytes` | gauge | Total RAM |
| `custom_memory_available_bytes` | gauge | RAM available to applications |
| `custom_memory_used_bytes` | gauge | Calculated used RAM |
| `custom_memory_usage_percent` | gauge | RAM usage percentage |

Used memory is calculated from `MemTotal - MemAvailable`.

## Swap

Source: `/proc/meminfo`

| Metric | Type | Meaning |
|---|---|---|
| `custom_swap_total_bytes` | gauge | Total Swap |
| `custom_swap_free_bytes` | gauge | Free Swap |
| `custom_swap_used_bytes` | gauge | Used Swap |

## Filesystem

Source: `df -B1 /`

The `-B1` option requests filesystem sizes in bytes.

| Metric | Type | Meaning |
|---|---|---|
| `custom_disk_total_bytes{mountpoint="/"}` | gauge | Total root filesystem size |
| `custom_disk_used_bytes{mountpoint="/"}` | gauge | Used root filesystem space |
| `custom_disk_free_bytes{mountpoint="/"}` | gauge | Available root filesystem space |
| `custom_disk_usage_percent{mountpoint="/"}` | gauge | Root filesystem usage percentage |

## Disk I/O

Source: `/proc/diskstats`

| Metric | Type | Meaning |
|---|---|---|
| `custom_disk_reads_completed_total` | counter | Completed read operations |
| `custom_disk_writes_completed_total` | counter | Completed write operations |
| `custom_disk_read_bytes_total` | counter | Total bytes read since boot |
| `custom_disk_written_bytes_total` | counter | Total bytes written since boot |

These are cumulative counters, so rate-style PromQL queries can be used to derive operations or throughput over time.

## System

Sources: `/proc/uptime`, `/proc/loadavg`, CPU information

| Metric | Type | Meaning |
|---|---|---|
| `custom_system_uptime_seconds` | gauge | Seconds since system boot |
| `custom_system_load1` | gauge | 1-minute load average |
| `custom_system_load5` | gauge | 5-minute load average |
| `custom_system_load15` | gauge | 15-minute load average |
| `custom_cpu_core_count` | gauge | Available CPU cores |

## Prometheus text format example

```text
# HELP custom_cpu_usage_percent Current total CPU usage percentage.
# TYPE custom_cpu_usage_percent gauge
custom_cpu_usage_percent 3.42
```

A labelled metric example:

```text
custom_disk_free_bytes{mountpoint="/"} 3942416384
```
