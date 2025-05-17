# SigNoz Docker Monitoring Fix

## Background

SigNoz includes the capability to monitor Docker containers via the OpenTelemetry (OTEL) Collector. However, the default setup in this repository had two main issues:

1. Logs from critical Docker containers were being filtered out unintentionally by the OTEL collector.
2. The Docker Compose file was using outdated versions for key services (`query-service` and `frontend`), causing compatibility issues with Docker monitoring.

---

## Issues Identified

### 1. Log Filtering in OTEL Collector Configuration

The `otel-collector-config.yaml` contained a filter that **removed** logs from important containers like `logspout`, `frontend`, `alertmanager`, `query-service`, `otel-collector`, `clickhouse`, and `zookeeper`.

Excerpt from the original config:

```yaml
receivers:
  - type: filter
    id: signoz_logs_filter
    expr: 'attributes.container_name matches "^signoz-(logspout|frontend|alertmanager|query-service|otel-collector|clickhouse|zookeeper)"'
```

This caused logs from these containers to be excluded.

---

### 2. Outdated Service Versions in Docker Compose

In `clickhouse-setup/docker-compose-minimal.yaml`, the following services were pinned to older versions:

```yaml
query-service:
  image: signoz/query-service:${DOCKER_TAG:-0.56.0}

frontend:
  image: signoz/frontend:${DOCKER_TAG:-0.56.0}
```

---

## Fixes Applied

### 1. Disabled Log Filtering in OTEL Collector

The filter section was commented out to stop removing logs from the key containers:

```yaml
# - type: filter
#   id: signoz_logs_filter
#   expr: 'attributes.container_name matches "^signoz-(logspout|frontend|alertmanager|query-service|otel-collector|clickhouse|zookeeper)"'
```

### 2. Updated Service Versions in Docker Compose

The `query-service` and `frontend` images were updated to the latest stable version `0.73.0`:

```yaml
query-service:
  image: signoz/query-service:${DOCKER_TAG:-0.73.0}

frontend:
  image: signoz/frontend:${DOCKER_TAG:-0.73.0}
```

---

## Results

- Logs from Docker containers are now properly collected and displayed in SigNoz.
- OTEL Collector ingests logs from all relevant containers without filtering.
- Updated service versions improve compatibility and stability.

---

## Usage

```bash
ansible-playbook up.yml
```

This does docker compose up on an "empty" signoz in a codespace. Use this as a starting point for instrumenting your app.

```bash
ansible-playbook down.yml
```

4. Access the SigNoz UI and verify Docker container logs and metrics are visible.

---

## Conclusion

These fixes resolve the Docker monitoring issues by allowing log ingestion and using updated service versions, improving overall monitoring fidelity in SigNoz.
