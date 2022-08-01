# grafana-prometheus-traefik-cadvisor-node-exporter-flask-alertmanager

# 🚀 Local DNS Stack: Grafana with Prometheus and Traefik using localdns 🚀

https://github.com/coding-to-music/grafana-prometheus-traefik-cadvisor-node-exporter-flask-alertmanager

From / By Ruan Bekker https://github.com/ruanbekker

https://github.com/ruanbekker/monitoring-grafana-prometheus-local

## Environment variables:

```java

```

## GitHub

```java
git init
git add .
git remote remove origin
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:coding-to-music/grafana-prometheus-traefik-cadvisor-node-exporter-flask-alertmanager.git
git push -u origin main
```

## Results:

I don't see any output on the ports, not sure how to view the results

http://localhost:3000

```
docker ps -a
```

Output:

```
CONTAINER ID   IMAGE                                                                            COMMAND                  CREATED         STATUS         PORTS                                       NAMES
48824fa33689   grafana/grafana                                                                  "/run.sh"                7 minutes ago   Up 7 minutes   3000/tcp                                    grafana
dc8797f2537d   grafana-prometheus-traefik-cadvisor-node-exporter-flask-alertmanager_flask-app   "python3 /src/app.py"    7 minutes ago   Up 7 minutes                                               flask-app
2c038980d0eb   prom/alertmanager                                                                "/bin/alertmanager -…"   7 minutes ago   Up 7 minutes   9093/tcp                                    alertmanager
8a1e4ad38b87   grafana/loki:2.0.0                                                               "/usr/bin/loki -conf…"   7 minutes ago   Up 7 minutes   0.0.0.0:3100->3100/tcp, :::3100->3100/tcp   loki
a3de8772ff7c   prom/node-exporter                                                               "/bin/node_exporter …"   7 minutes ago   Up 7 minutes   9100/tcp                                    node-exporter
a57acf0b80be   google/cadvisor                                                                  "/usr/bin/cadvisor -…"   7 minutes ago   Up 7 minutes   8080/tcp                                    cadvisor
d37cb4462ea9   traefik:1.7.14                                                                   "/traefik --docker -…"   7 minutes ago   Up 7 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp           traefik
f7851498135e   prom/prometheus                                                                  "/bin/prometheus --c…"   7 minutes ago   Up 7 minutes   9090/tcp                                    prometheus
```

```
docker-compose stop
```

Output:

```
Stopping grafana       ... done
Stopping flask-app     ... done
Stopping alertmanager  ... done
Stopping loki          ... done
Stopping node-exporter ... done
Stopping cadvisor      ... done
Stopping traefik       ... done
```

# monitoring-grafana-prometheus-local

This is a local stack that you can boot with docker-compose which will give you: Traefik, Grafana, Prometheus, cAdvisor, Node-Exporter, AlertManager, Python App instrumented with Prometheus

## Contents

Stack will give you:

- Traefik: Reverse Proxy and domain configured is `*.localdns.xyz` which all resolves to 127.0.0.1
- Grafana: Dashboarding `grafana.localdns.xyz`
- Prometheus: Time Series DB `prometheus.localdns.xyz`
- Node-Exporter: Node Level Metrics
- cAdvisor: Container Level Metrics
- Alertmanager: Creates Alerts from Prometheus Metrics `alertmanager.localdns.xyz`
- Python Flask App: Instrumented with Prometheus `flask-app.localdns.xyz`

## Usage

We are using loki to ship logs from our containers, so we need to install the loki docker driver:

```
$ docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

To boot your stack in localhost mode (ie. traefik.localdns.xyz -> 127.0.0.1):

```
$ docker-compose up --build -d
```

To boot your stack in ip mode (ie. traefik.10.2.3.4.nip.io -> 10.2.3.4):

```
$ IP=$(curl -s -4 ifconfig.co)
$ export DOMAIN=${IP}.nip.io
$ docker-compose up --build -d
```

Access [http://traefik.localdns.xyz](http://traefik.localdns.xyz) for viewing the Traefik Dashboard, or [grafana.localdns.xyz](http://grafana.localdns.xyz) to access Grafana and 2 pre-made dashboards will be visible to view node and container level metrics.

The default user/pass is admin/admin

Access [prometheus.localdns.xyz](http://prometheus.localdns.xyz) to access Prometheus UI

For the web application you can access it on [flask-app.localdns.xyz](http://flask-app.localdns.xyz), and has the following endpoints:

- `/`
- `/wait/{int}` - how long the request should take in seconds
- `/status/{int}` - the status code that should be captured
- `/skip` - does not collect metrics
- `/metrics` view the metrics that is exporter for prometheus to scrape

For more detailed alerting have a look at my [prometheus-alerting-alertmanager](https://github.com/ruanbekker/prometheus-alerting-alertmanager) repo:

- [alertmanager.yml](https://github.com/ruanbekker/prometheus-alerting-alertmanager/blob/main/alertmanager/alertmanager.yml)
- [host_alerts.rules](https://github.com/ruanbekker/prometheus-alerting-alertmanager/blob/main/prometheus-a/rules/host_alert.rules)

## Resources

- [Prometheus Recording Rules](https://deploy.live/blog/today-i-learned-prometheus-recording-rules/) and [another example](https://medium.com/javarevisited/create-recording-rules-in-prometheus-8a6a1c0b9e11)
