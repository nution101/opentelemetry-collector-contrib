[comment]: <> (Code generated by mdatagen. DO NOT EDIT.)

# cpu

## Default Metrics

The following metrics are emitted by default. Each of them can be disabled by applying the following configuration:

```yaml
metrics:
  <metric_name>:
    enabled: false
```

### system.cpu.time

Total seconds each logical CPU spent on each mode.

| Unit | Metric Type | Value Type | Aggregation Temporality | Monotonic |
| ---- | ----------- | ---------- | ----------------------- | --------- |
| s | Sum | Double | Cumulative | true |

#### Attributes

| Name | Description | Values | Optional |
| ---- | ----------- | ------ | -------- |
| cpu | Logical CPU number starting at 0. | Any Str | false |
| state | Breakdown of CPU usage by type. | Str: ``idle``, ``interrupt``, ``nice``, ``softirq``, ``steal``, ``system``, ``user``, ``wait`` | false |

## Optional Metrics

The following metrics are not emitted by default. Each of them can be enabled by applying the following configuration:

```yaml
metrics:
  <metric_name>:
    enabled: true
```

### system.cpu.frequency

Current frequency of the CPU core in Hz.

| Unit | Metric Type | Value Type |
| ---- | ----------- | ---------- |
| Hz | Gauge | Double |

#### Attributes

| Name | Description | Values | Optional |
| ---- | ----------- | ------ | -------- |
| cpu | Logical CPU number starting at 0. | Any Str | false |

### system.cpu.logical.count

Number of available logical CPUs.

| Unit | Metric Type | Value Type | Aggregation Temporality | Monotonic |
| ---- | ----------- | ---------- | ----------------------- | --------- |
| {cpu} | Sum | Int | Cumulative | false |

### system.cpu.physical.count

Number of available physical CPUs.

| Unit | Metric Type | Value Type | Aggregation Temporality | Monotonic |
| ---- | ----------- | ---------- | ----------------------- | --------- |
| {cpu} | Sum | Int | Cumulative | false |

### system.cpu.utilization

Difference in system.cpu.time since the last measurement per logical CPU, divided by the elapsed time (value in interval [0,1]).

| Unit | Metric Type | Value Type |
| ---- | ----------- | ---------- |
| 1 | Gauge | Double |

#### Attributes

| Name | Description | Values | Optional |
| ---- | ----------- | ------ | -------- |
| cpu | Logical CPU number starting at 0. | Any Str | false |
| state | Breakdown of CPU usage by type. | Str: ``idle``, ``interrupt``, ``nice``, ``softirq``, ``steal``, ``system``, ``user``, ``wait`` | false |
