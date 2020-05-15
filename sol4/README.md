### sol4

* alert.rules : p1/etc/prometheus/
```
groups:
- name: example
  rules:
  - alert: cpuUsge
    expr: 100 - (avg by (instance) (irate(node_cpu_seconds_total{job='servers',mode='idle'}[3m])) * 100) > 90
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: Machine under healvy load
```
* prometheus.yml : p1/etc/prometheus/
```

```
* alertmanager.yml : s2/etc/alertmanager/
```
global:
templates:
- '/etc/alertmanager/template/*.tmpl'
route:
  repeat_interval: 1m
  receiver: operations-team

receivers:
- name: 'operations-team'
  slack_configs:
  - api_url: https://hooks.slack.com/services/xxxxxxxx
    channel: '#xalert'
    send_resolved: true
```
