**Downloading and Installing Node-Exporter on Client Nodes**

1. Use below wget command to download the stable release of node exporter.

wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz

2. Extract the application and move the .exe file to /usr/local/bin for everyone to be able to access it.

tar xvfz node_exporter-*.tar.gz
sudo mv node_exporter-1.5.0.linux-amd64/node_exporter /usr/local/bin

3. To run Node Exporter as a service follow below steps starting with creation of a user

sudo useradd -rs /bin/false node_exporter

4. Create a service file for systemctl to use. The file must be named node_exporter.service and should have the following format. Most of the fields are similar to those found in prometheus.service, as described in the prometheus installation guide.

sudo nano /etc/systemd/system/node_exporter.service
----------------------------------------------------------------------------------------------------
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
---------------------------------------------------------------------------------------------------

5. Run following commands to reload system daemon and enable node exporter service.

sudo systemctl enable node_exporter
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl status node_exporter

6. Use a web browser to visit port 9100 on the client node, for example, http://local_ip_addr:9100. A page entitled Node Exporter is displayed along with a link reading Metrics. Click the Metrics link and confirm the statistics are being collected.

7. Repeat same steps for all the nodes which you would like to monitor.