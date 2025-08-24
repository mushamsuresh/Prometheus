# ✅ Prometheus Setup as a Systemd Service

Follow these steps to install and run Prometheus as a service on Linux.

---

## 1. Create a dedicated Prometheus user

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

---

## 2. Create directories for config and data

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

---

## 3. Move Prometheus binaries to `/usr/local/bin`

```bash
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
```

---

## 4. Move configuration file & set permissions

```bash
sudo cp prometheus.yml /etc/prometheus/
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
```

---

## 5. Create the systemd service file

Edit the file:

```bash
sudo vi /etc/systemd/system/prometheus.service
```

Paste this content:

```ini
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
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.listen-address=0.0.0.0:9090

Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Save & exit.

---

## 6. Reload systemd and start Prometheus

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

---

## 7. Verify Prometheus service

Check status:

```bash
sudo systemctl status prometheus
```

If running successfully, open your browser at:

```
http://localhost:9090
```

You should see the **Prometheus Web UI** 🎉
