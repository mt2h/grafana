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
curl "127.0.0.1:3100/metrics"
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

ls -lh /var/log/nginx/ #allow adm
usermod -a -G adm promtail
groups promtail
```

# install prometheus

```bash
apt install prometheus
service prometheus status
ps -u prometheus
curl localhost:9100/metrics
```

# install influxdb

```bash
apt-cache policy influxdb
wget -qO- https://repos.influxdata.com/influxdb.key | apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | tee /etc/apt/sources.list.d/influxdb.list
apt update
apt-cache policy influxdb
apt install influxdb -y
systemctl start influxdb
systemctl status influxdb
systemctl enable influxdb
ps -u influxdb
curl localhost:8086/metrics

influx
> create database telegraf
> show databases
> create user grafana with password 'password'
> grant READ on telegraf to grafana
> show users
> show GRANTS for grafana
> create user telegraf with password 'password'
> show users
> grant ALL on telegraf to telegraf
> show GRANTS for telegraf
> use telegraf
> show measurements
> quit
```

# install telegraf

```bash
apt install telegraf -y
systemctl start telegraf
systemctl status telegraf
systemctl enable telegraf
ps -u telegraf

telegraf -test -config /etc/telegraf/telegraf.conf
service telegraf restart
service telegraf status
```

# firewall

```bash
#loki
iptables -A INPUT -p tcp -s localhost --dport 3100 -j ACCEPT
iptables -A INPUT -p tcp --dport 3100 -j DROP
iptables -L

#promtail
iptables -A INPUT -p tcp -s localhost --dport 9080 -j ACCEPT
iptables -A INPUT -p tcp --dport 9080 -j DROP

#prometheus
iptables -A INPUT -p tcp -s localhost --dport 9090 -j ACCEPT
iptables -A INPUT -p tcp --dport 9090 -j DROP
iptables -L

#node-exporter:
iptables -A INPUT -p tcp -s 192.168.10.202 --dport 9100 -j ACCEPT
iptables -A INPUT -p tcp --dport 9100 -j DROP
iptables -L

#influxdb
iptables -A INPUT -p tcp -s localhost --dport 8086 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j DROP

#persistent saved
apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
iptables-save > /etc/iptables/rules.v6
```
# board

![Board](./img/board.jpeg