---
namespace: odem
collection: services
role: monitoring
---

# `odem.services.monitoring`

Deploy the `mps-services-monitoring` stack — Prometheus (metrics), Loki (logs),
and Grafana (dashboards) — via its docker compose file: clone the repo,
render `.env` from variables, materialize the gitignored service config
files from their `.example` siblings, prepare the bind-mounted data
directories, and start the stack.

An optional **Wazuh** SIEM (manager, indexer, dashboard) can be added by
setting `monitoring_enable_wazuh: true`; it is off by default. With both
`monitoring_enable_wazuh` and `monitoring_enable_setup` enabled, the role runs
the repo's `scripts/wazuh-setup.sh` (TLS certificates, `vm.max_map_count`)
before starting the stack — the Wazuh containers do not start without it.

Docker and Docker Compose must already be present on the host
(see [`odem.services.docker`](../docker/README.md)).

## Default variables

| Variable | Default | Description |
|---|---|---|
| `monitoring_enable_service` | `true` | Bring the compose stack up at the end of the run |
| `monitoring_enable_setup` | `true` | Run the repo's setup.sh to create and chown the data directories |
| `monitoring_enable_wazuh` | `false` | Start the Wazuh SIEM containers via the `wazuh` compose profile |
| `monitoring_repo_url` | `https://github.com/my-perfect-system/mps-services-monitoring.git` | Git URL for the mps-services-monitoring repo |
| `monitoring_repo_version` | `main` | Git ref to check out |
| `monitoring_repo_dest` | `{{ user_default_repo_dir }}/mps-services-monitoring` | Clone destination |
| `monitoring_bind_ip` | `127.0.0.1` | Host IP the service ports bind to |
| `monitoring_grafana_port` | `3000` | Host port for Grafana |
| `monitoring_prometheus_port` | `9090` | Host port for Prometheus |
| `monitoring_loki_port` | `3100` | Host port for Loki |
| `monitoring_grafana_admin_user` | `admin` | Grafana admin username |
| `monitoring_grafana_admin_password` | `admin` | Grafana admin password |
| `monitoring_prometheus_version` | `v3.12.0` | Prometheus image tag |
| `monitoring_loki_version` | `3.7.2` | Loki image tag |
| `monitoring_grafana_version` | `13.0.2` | Grafana image tag |
| `monitoring_wazuh_version` | `5.0.0-beta5` | Wazuh image tag (manager, indexer, dashboard) |
| `monitoring_wazuh_packages` | `[openssl]` | Packages required by the Wazuh setup script |
| `monitoring_wazuh_indexer_heap_gb` | `1` | JVM heap size in GB for the Wazuh indexer |
| `monitoring_wazuh_agent_port` | `1514` | Host port for agent event traffic (TCP) |
| `monitoring_wazuh_enrollment_port` | `1515` | Host port for agent enrollment (TCP) |
| `monitoring_wazuh_tls_port` | `1517` | Host port for the manager TLS agent listener (TCP) |
| `monitoring_wazuh_syslog_port` | `514` | Host port for syslog ingestion (UDP) |
| `monitoring_wazuh_api_port` | `55000` | Host port for the manager API (TCP) |
| `monitoring_wazuh_dashboard_port` | `443` | Host port for the dashboard https UI |
| `monitoring_wazuh_manager_indexer_user` | `wazuh-manager` | Indexer user the manager authenticates with |
| `monitoring_wazuh_manager_indexer_password` | `wazuh-manager` | Password for the manager's indexer user |
| `monitoring_wazuh_dashboard_user` | `kibanaserver` | Indexer user the dashboard authenticates with |
| `monitoring_wazuh_dashboard_password` | `kibanaserver` | Password for the dashboard's indexer user |

## Dependencies

- `odem.os.user`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.services.docker
    - odem.services.monitoring
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)