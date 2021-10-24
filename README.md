# install grafana

```bash
apt-get install -y adduser libfontconfig1
wget https://dl.grafana.com/oss/release/grafana_7.1.3_amd64.deb
dpkg -i grafana_7.1.3_amd64.deb
service grafana-server start
service grafana-server status
```

# install loki
```bash
curl -fSL -o loki.gz "https://github.com/grafana/loki/releases/download/v1.6.1/loki-linux-amd64.zip"
gunzip loki.gz
chmod a+x loki
useradd --system loki
```

#install promtail
```bash
curl -fSL -o promtail.gz "https://github.com/grafana/loki/releases/download/v1.6.1/promtail-linux-amd64.zip"
gunzip promtail.gz
chmod a+x promtail
```
