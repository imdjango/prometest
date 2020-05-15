### sol4

* alert.rules; [p1] /etc/prometheus/
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
* prometheus.yml; [p1] /etc/prometheus/
```
# rule files and alertmanagers:
rule_files:
- "/etc/prometheus/alert.rules"

alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - "s2:9093"

# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']
    
  - job_name: 'servers'
    static_configs:
    - targets: ['s1:9100','s2:9100']

  - job_name: 'web'
    static_configs:
    - targets: ['s2:5000']
```
* alertmanager.yml; [s2] /etc/alertmanager/
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
