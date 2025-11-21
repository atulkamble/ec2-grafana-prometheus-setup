# Grafana Server
Installation of grafana server 
t3.medium | SG-Inbound | 3000 | 9090 | 9100
```
sudo yum update -y
sudo yum install wget tar -y
sudo yum install make -y
sudo yum install -y https://dl.grafana.com/grafana-enterprise/release/12.2.1/grafana-enterprise_12.2.1_18655849634_linux_amd64.rpm
sudo systemctl start grafana-server
sudo systemctl enable  grafana-server
sudo systemctl status grafana-server

http://18.232.59.17:3000/

>> admin,admin

>> Admin@123

// install prometheus 

wget https://prometheus.io/download/3.7.3/2025-10-29/prometheus-3.7.3.linux-amd64.tar.gz

sudo yum install collectd-write_prometheus.x86_64 -y

scp -i /Users/atul/Downloads/garafana.pem \
/Users/atul/Downloads/prometheus-3.7.3.linux-amd64.tar.gz \
ec2-user@18.232.59.17:/home/ec2-user/

sudo tar -xvf prometheus-3.7.3.linux-amd64.tar.gz
sudo mv prometheus-3.7.3.linux-amd64 prometheus
sudo useradd --no-create-home --shell /bin/false prometheus

// optional
id
sudo cat /etc/passwd
sudo cat /etc/group

cd prometheus
sudo cp -r prometheus /usr/local/bin/
sudo cp -r promtool /usr/local/bin/

sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo cp prometheus.yml /etc/prometheus/

sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

# **Configure systemd Service**

Create the service file:

```sh
sudo nano /etc/systemd/system/prometheus.service
```

Paste this:

```
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

Save & exit.

---

# **Start and Enable Prometheus**

```sh
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
sudo systemctl status prometheus
```

---

# **🔹 Access Prometheus UI**

Open in browser:

```
http://<EC2_PUBLIC_IP>:9090
```

# Node Exporter
// download node exporter 

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
tar xvf node_exporter-1.10.2.linux-amd64.tar.gz
cd node_exporter-1.10.2.linux-amd64
./node_exporter
```

OR

```
sudo cp node_exporter /usr/local/bin
sudo useradd node_exporter --no-create-home --shell /bin/false
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```
// edit node exporter service 
```
sudo nano /etc/systemd/system/node_exporter.service
```
// copy following syntax and paste 

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter
```

```
http://instance-ip:9100/metrics

http://44.192.38.111:9100/metrics
```

---

