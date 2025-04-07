# INSTALLING PROMETHEUS (DPKG METHOD)

**1 - First we will create a group and a system user for Prometheus; following this command:**

```
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus
```

**2 - Create a DIR to put the archives of prometheus (download the latest version) copying "copy link adress" on prometheus link:**

```
 mkdir prometheus && cd prometheus
 wget https://github.com/prometheus/prometheus/releases/download/v2.53.4/prometheus-2.53.4.linux-amd64.tar.gz
```

**3 - After the download we need extract the contents of file:**
``` 
tar xvf prometheus-2.53.4.linux-amd64.tar.gz
```

**4 - Now we need to move binary files (prometheus and promtool) and change the ownership of files to the "prometheus" user and group:**
```
sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

**5- Next, we move the configuration files and set their ownership so that Prometheus can access them:**
```
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
```
```
sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
```

**6- Now we will create a service file to prometheus:**
```
sudo vi /etc/systemd/system/prometheus.service
and put the content:
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

**7 - Reload, Enable, Start and check the service:**
```
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus
```

**8 - allow the port 9090 (default prometheus port) and we can access:**
```
sudo ufw allow 9090/tcp #to allow the port
```

**9 - finally we can access:**
<http://localhost:9090>


