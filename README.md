# install grafana

```bash
apt-get install -y adduser libfontconfig1
wget https://dl.grafana.com/oss/release/grafana_7.1.3_amd64.deb
dpkg -i grafana_7.1.3_amd64.deb
service grafana-server start
service grafana-server enable
service grafana-server status
```

# install loki
```bash
curl -fSL -o loki.gz "https://github.com/grafana/loki/releases/download/v1.6.1/loki-linux-amd64.zip"
gunzip loki.gz
chmod a+x loki
useradd --system loki
service loki start
service loki enable
service loki status
```

# install promtail
```bash
curl -fSL -o promtail.gz "https://github.com/grafana/loki/releases/download/v1.6.1/promtail-linux-amd64.zip"
gunzip promtail.gz
chmod a+x promtail
usermod -a -G systemd-journal promtail
useradd --system promtail
service promtail start
service promtail enable
service promtail status
curl "127.0.0.1:9080/metrics"
chown promtail:promtail /tmp/positions.yaml
```

# firewall
```bash
#loki
iptables -A INPUT -p tcp -s localhost --dport 3100 -j ACCEPT
iptables -A INPUT -p tcp --dport 3100 -j DROP
iptables -L
curl "127.0.0.1:3100/metrics"
#promtail
iptables -A INPUT -p tcp -s localhost --dport 9080 -j ACCEPT
iptables -A INPUT -p tcp --dport 9080 -j DROP

apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
iptables-save > /etc/iptables/rules.v6
```