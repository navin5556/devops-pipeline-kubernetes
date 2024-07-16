### Links to download Prometheus, Node_Exporter & black Box exporter https://prometheus.io/download/
### Links to download Grafana https://grafana.com/grafana/download
### Other link from video https://github.com/prometheus/blackbox_exporter
```
-----promethious-----

sudo apt update -y
wget https://github.com/prometheus/prometheus/releases/download/v2.53.1/prometheus-2.53.1.linux-amd64.tar.gz
tar -xvf prometheus-2.53.1.linux-amd64.tar.gz
rm -rf prometheus-2.53.1.linux-amd64.tar.gz
cd prometheus-2.53.1.linux-amd64/
./prometheus &

http://3.110.58.232:9090/


-----graphana-----

sudo apt-get install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_11.1.0_amd64.deb
sudo dpkg -i grafana-enterprise_11.1.0_amd64.deb
sudo /bin/systemctl start grafana-server
http://3.110.58.232:3000/login

-----nlackbox exporter---- 
# this will help us to monitor the websites
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.25.0/blackbox_exporter-0.25.0.linux-amd64.tar.gz
tar -xvf blackbox_exporter-0.25.0.linux-amd64.tar.gz
rm -rf blackbox_exporter-0.25.0.linux-amd64.tar.gz
cd blackbox_exporter-0.25.0.linux-amd64
./blackbox_exporter &
http://3.110.58.232:9115/

The blackbox exporter needs to be passed the target as a parameter, this can be done with relabelling.

Example config:

scrape_configs:
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - http://prometheus.io    # Target to probe with http.
        - https://prometheus.io   # Target to probe with https.
        - http://example.com:8080 # Target to probe with http on port 8080.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 3.110.58.232:9115  # The blackbox exporter's real hostname:port.
  - job_name: 'blackbox_exporter'  # collect blackbox exporter's operational metrics.
    static_configs:
      - targets: ['3.110.58.232:9115']


vim prometheus.yml


now get the if of promethious process and kill the id
ubuntu@ip-172-31-29-251:~/prometheus-2.53.1.linux-amd64$ pgrep prometheus
18246
kill 18246
./prometheus &
now we will add promethious as a datasource inside the grafana
now search "blackbox grafana dashboard" and add the copied id into grafana import dashboard


now we want to monitor jenkins
for that installed plugin - "Prometheus metricsVersion"
now installed node exporter on jenkins
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar -xvf node_exporter-1.8.2.linux-amd64.tar.gz
rm -rf node_exporter-1.8.2.linux-amd64.tar.gz
cd node_exporter-1.8.2.linux-amd64
./node_exporter &
http://3.110.58.232:9100/


now add below code in promethious.yaml file

- job_name: 'node_exporter'
  static_configs:
  - targets: ['3.7.57.150:9100']
- job_name: 'jenkins'
  metrics_path: '/prometheus'
  static_configs:
  - targets: ['3.7.57.150:8080']
  now get the if of promethious process and kill the id
ubuntu@ip-172-31-29-251:~/prometheus-2.53.1.linux-amd64$ pgrep prometheus
18246
kill 18246
./prometheus &
now we will add nodeexporter as a datasource inside the grafana
now search "node exporter grafana dashboard" and add the copied id into grafana import dashboard


i'll node-exporter for monitoring my application


```