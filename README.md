# dark

Grafana dashboards are often maintained separately from the services and environments they monitor. `dark` lets teams define dashboards as Kubernetes resources, so dashboard configuration can live beside application manifests and move through the same deployment workflow.

## Features

- Define Grafana dashboards with YAML-backed custom resources.
- Apply dashboard resources with `kubectl`.
- Manage Grafana data sources from Kubernetes resources.
- Create and manage Grafana API keys.
- Configure alert routing and notification policies.
- Use [Grabana](https://github.com/K-Phoen/grabana) for dashboard decoding and generation.

## Installation

Build `dark` with Go from a checkout of this repository:

```sh
go install ./...
```

To build a local binary instead:

```sh
go build -o dark .
```

`dark` manages Grafana configuration through Kubernetes. The Grafana instance and its Kubernetes integration must be configured separately.

## Usage

Run the built-in help to see the options available in your build:

```sh
./dark --help
```

A dashboard can be declared as a Kubernetes resource:

```yaml
apiVersion: k8s.kevingomez.fr/v1
kind: GrafanaDashboard
metadata:
  name: example-dashboard
  namespace: monitoring
  annotations:
    dark/folder: "Awesome folder"
spec:
  title: Awesome dashboard
  shared_crosshair: true
  tags: [generated, yaml]
  auto_refresh: 10s
  rows:
    - name: Prometheus
      panels:
        - timeseries:
            title: HTTP Rate
            datasource: prometheus-default
            targets:
              - prometheus:
                  query: "rate(promhttp_metric_handler_requests_total[5m])"
                  legend: "{{ handler }} - {{ code }}"
```

Apply the resource with `kubectl`:

```sh
kubectl apply -f dashboard.yaml
```

See the project documentation and `examples/` directory for additional resource definitions and configuration details.

## License

`dark` is licensed under the [MIT License](LICENSE). This project is based on [K-Phoen/dark](https://github.com/K-Phoen/dark); the original project and its MIT attribution are retained here.
