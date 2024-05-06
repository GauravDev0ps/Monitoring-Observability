**Downloading and Installing Prometheus on Monitoring Server**

1. Visit the Prometheus downloads and make a note of the most recent release. The most recent LTS release is clearly indicated on their website.

2. Use wget utility to download Prometheus to the monitoring server. Use below command to download:

 wget https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz

3. Unzip downloaded package

tar xvfz prometheus-*.tar.gz

4. Create "/etc/prometheus" and "/var/lib/prometheus" directories for the prometheus as "/etc/prometheus" directory stores the Prometheus configuration files. The "/var/lib/prometheus" directory holds application data.

sudo mkdir /etc/prometheus /var/lib/prometheus

5. Move the prometheus and promtool directories to the /usr/local/bin/ directory. This makes Prometheus accessible to all users.

sudo mv prometheus promtool /usr/local/bin/

6. Move the prometheus.yml YAML configuration file to the /etc/prometheus directory.

sudo mv prometheus.yml /etc/prometheus/prometheus.yml

7. The consoles and console_libraries directories contain the resources necessary to create customized consoles. Move these files to the etc/prometheus directory.

sudo mv consoles/ console_libraries/ /etc/prometheus/

8. Verify that Prometheus is successfully installed using the below command:

prometheus --version

9. To configure prometheus as a service, create a prometheus user. The following command creates a system user.

sudo useradd -rs /bin/false prometheus

10. Assign ownership of the two directories created to the new prometheus user.

sudo chown -R prometheus: /etc/prometheus /var/lib/prometheus

11. To allow Prometheus to run as a service, create a prometheus.service file using the following command:

sudo nano /etc/systemd/system/prometheus.service

Enter the following content into the file:
---------------------------------------------------------------------------------------------------
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries \
    --web.listen-address=0.0.0.0:9090 \
    --web.enable-lifecycle \
    --log.level=info

[Install]
WantedBy=multi-user.target
---------------------------------------------------------------------------------------------------

*- The Wants and After options must be set to network-online.target.
- The User and Group fields must both be set to prometheus.
- The ExecStart parameter explains where to find the prometheus executable and defines the default  options.
- The config.file option defines the location of the Prometheus configuration file as /etc/- - prometheus/prometheus.yml.
- storage.tsdb.path tells Prometheus to store application data in the /var/lib/prometheus/ directory.
- web.listen-address is set to 0.0.0.0:9090, allowing Prometheus to listen for connections on all network interfaces.
- The web.enable-lifecycle option allows users to reload the configuration file without restarting Prometheus.*

12. Run following commands to reload system daemon and enable prometheus service.

sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus

13. Confirm the status of service as running and enabled. Now you can access Prometheus web interface and dashboard at http://local_ip_addr:9090. Replace local_ip_addr with the address of the monitoring server

14. The default prometheus.yml file contains a directive to scrape the local host. Click Status and Targets to list all the targets. Prometheus should display the local Prometheus service as the only target.

15. Now in order to configure prometheus to monitor client nodes, edit the prometheus.yml file.

16. Locate the section entitled "scrape_configs", which contains a list of jobs. It currently lists a single job named prometheus. This job monitors the local Prometheus task on port 9090. Beneath the prometheus job, add a second job having the job_name of Node_1. Include the following information.
--------------------------------------------------------------------------------------------------
...
- job_name: "Node_1"
  scrape_interval: 10s
  static_configs:
    - targets: ["node_ip_addr:9100"]
----------------------------------------------------------------------------------------------------


17. Add the same block for other nodes as well in the same syntax. Now, to immediately refresh Prometheus, restart the prometheus service.

sudo systemctl restart prometheus

18. Using a web browser, revisit the Prometheus web portal at port 9090 on the monitoring server. Select Status and then Targets. A second link for the remote_collector job is displayed, leading to port 9100 on the client. Click the link to review the statistics.

