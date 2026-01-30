GRAFANA


# 📊 Grafana OSS Installation on Ubuntu (Official Method)

This guide explains how to install **Grafana OSS** on an **Ubuntu Server** using the **official APT repository**.
This is the **recommended, production-safe method**.

---

## 🧩 Prerequisites

* Ubuntu 20.04 / 22.04 / 24.04
* User with `sudo` privileges
* Internet access

---

## 🚀 Installation Steps

### Step 1: Update the system

```bash
sudo apt update
sudo apt install -y apt-transport-https software-properties-common wget
```

---

### Step 2: Add Grafana GPG key

```bash
sudo mkdir -p /etc/apt/keyrings
wget -q -O - https://apt.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.asc
```

---

### Step 3: Add Grafana APT repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

---

### Step 4: Install Grafana OSS

```bash
sudo apt update
sudo apt install -y grafana
```

---

### Step 5: Start and enable Grafana service

```bash
sudo systemctl daemon-reexec
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

---

### Step 6: Verify Grafana service

```bash
sudo systemctl status grafana-server
```

Check if Grafana is listening on port **3000**:

```bash
ss -tulpn | grep 3000
```

---

## 🌐 Access Grafana UI

Open a browser and navigate to:

```
http://<SERVER-IP>:3000
```

### Default credentials

* **Username:** `admin`
* **Password:** `admin`

PROMETHUS



## ✅ Prometheus Installation on Ubuntu Server

### 1️⃣ Update system

```bash
sudo apt update
sudo apt install -y wget tar
```

---

### 2️⃣ Create Prometheus user (security best practice)

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

---

### 3️⃣ Create required directories

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

Set ownership:

```bash
sudo chown prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

---

### 4️⃣ Download Prometheus

```bash
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
```

Extract:

```bash
tar xvf prometheus-2.52.0.linux-amd64.tar.gz
```

---

### 5️⃣ Move binaries

```bash
sudo cp prometheus-2.52.0.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.52.0.linux-amd64/promtool /usr/local/bin/
```

Set ownership:

```bash
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

---

### 6️⃣ Move configuration files

```bash
sudo cp -r prometheus-2.52.0.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-2.52.0.linux-amd64/console_libraries /etc/prometheus
sudo cp prometheus-2.52.0.linux-amd64/prometheus.yml /etc/prometheus
```

Set ownership:

```bash
sudo chown -R prometheus:prometheus /etc/prometheus
```

---

### 7️⃣ Create systemd service

```bash
sudo vi /etc/systemd/system/prometheus.service
```

Paste this:

```ini
[Unit]
Description=Prometheus
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

### 8️⃣ Start & enable Prometheus

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

---

### 9️⃣ Verify Prometheus

```bash
sudo systemctl status prometheus
```

Check port:

```bash
ss -tulpn | grep 9090
```

---

## 🌐 Access Prometheus

Open browser:

```
http://<SERVER-IP>:9090
```

NODE EXPORTER
Perfect 👍 Node Exporter is **mandatory** with Prometheus in real DevOps setups.
Below are **clean, interview-ready steps** to install **Node Exporter on a Linux (Ubuntu) server**.

---

## ✅ Node Exporter Installation (Linux / Ubuntu)

### 1️⃣ Update system

```bash
sudo apt update
sudo apt install -y wget tar
```

---

### 2️⃣ Create node_exporter user (security best practice)

```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```

---

### 3️⃣ Download Node Exporter

```bash
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
```

---

### 4️⃣ Extract archive

```bash
tar xvf node_exporter-1.8.1.linux-amd64.tar.gz
```

---

### 5️⃣ Move binary

```bash
sudo cp node_exporter-1.8.1.linux-amd64/node_exporter /usr/local/bin/
```

Set ownership:

```bash
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

---

### 6️⃣ Create systemd service

```bash
sudo vi /etc/systemd/system/node_exporter.service
```

Paste this:

```ini
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

Save & exit.

---

### 7️⃣ Start & enable Node Exporter

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

---

### 8️⃣ Verify Node Exporter

```bash
sudo systemctl status node_exporter
```

Check port:

```bash
ss -tulpn | grep 9100
```

---

## 🌐 Access Node Exporter

Open browser:

```
http://<SERVER-IP>:9100/metrics
```
🔗 Next Step: Add Node Exporter to Prometheus

Edit Prometheus config:

sudo vi /etc/prometheus/prometheus.yml


Add under scrape_configs:

  - job_name: "node_exporter"
    static_configs:
      - targets: ["localhost:9100","localhostofnodeexporterfrombrowser:9100"]


Restart Prometheus: sudo systemctl restart prometheus


final go to grafana dashboard add sources in prometheus => import

GRAFANA DASHBOARDS
https://grafana.com/grafana/dashboards/
COPY ID FROM NODE EXPORTER
