**Downloading and Installing Grafana on Monitoring Server**

1. Install some required utilities using apt.

sudo apt-get install -y apt-transport-https software-properties-common

2. Import the Grafana GPG key.

sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key

3. Add the Grafana “stable releases” repository.

echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

4. Update the packages in the repository, including the new Grafana package.

sudo apt-get update -y

5. Install the open-source version of Grafana.

sudo apt-get install grafana

6. Reload the systemctl daemon and enable grafana server. The status of service must be in running state.

sudo systemctl daemon-reload
sudo systemctl enable grafana-server.service
sudo systemctl start grafana-server
sudo systemctl status grafana-server

7. Now, in order to integrate grafana with prometheus and cloudwatch; using a web browser, visit port 3000 of the monitoring server. For example, enter http://local_ip_addr:3000, replacing local_ip_addr with the actual IP address. Grafana displays the login page. Use the username admin and the default password password. Change the password to a more secure value when prompted to do so.

8. After a successful password change, Grafana displays the Grafana Dashboard.

9. To add Prometheus as a data source, click the gear symbol, standing for Configuration, then select Data Sources. At the next display, click the Add data source button.

10. Choose Prometheus as the data source.

11. For a local Prometheus source, as described in this guide, set the URL to http://localhost:9090 or enter the address of the prometheus server if installed on a different machine. Most of the other settings can remain at the default values. However, a non-default Timeout value can be added here.

12. When satisfied with the settings, select the Save & test button at the bottom of the screen.

13. If all settings are correct, Grafana confirms the Data source is working.

14. Follow the same steps for adding cloudwatch as a data source and authenticate using either security credentials or a credentials file.

15. Select all other default and necessary field as per your infrastructure.

16. To import a dashboard Visit the Grafana Dashboard Library. Enter Node exporter as the search term and select the entry for Node Exporter Full.

17. Make a note of the ID number or use the button to copy the ID to the clipboard. The ID of this board is currently 1860.

18. Return to the Grafana dashboard. Select the Dashboard icon, consisting of four squares, and choose + Import.

19. In the Import via grafana.com box, enter the ID 1860 from the previous step. Then select the Load button.

20. At the next screen, confirm the import details. Choose Prometheus as the data source and click the Import button.

21. The Node Exporter Full dashboard takes effect immediately. It displays the performance metrics and state of the client node, including the Memory, RAM, and CPU details. Several drop-down menus at the top of the screen allow users to select the host to observe and the time period to highlight.

22. Similarily for Cloudwatch, use the attached JSON templates to create dashboards for monitoring EC2 instances and Elastic load balancers in your infrastructure.