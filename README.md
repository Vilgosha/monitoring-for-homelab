# Monitoring for homelab

Monitoring was setup on Ubuntu server 25.04.

1) Create directory:

			mkdir monitoring

2) Place there 3 files:
   
		docker-compose.yml
	
  		prometheus.yml
	
  		grafana.ini

4) In grafana.ini put your own information.
   
	In my case I used gmail for alerts.

	#### Do not use your main email address!

	#### I would recommend to use App password from google!

	#### Password must be without spaces!

	user = example@gmail.com

	password = App_password

	from_name = example@gmail.com

4) Check prometheus.yml if any changes required for your setup.

5) Change in docker-compose.yml GF_SECURITY_ADMIN_PASSWORD 

6) Start yourn own monitoring system:

	docker compose up -d 

7) Open in browser localhost:3535 you should see there login into Grafana
   
	user: admin
	
	password: (see step 5) 

9) Try dashboard 893 for docker container.

10) Setup dashboards for CPU & RAM usage in Grafana.

	Code for RAM % usage

			(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

	Code for CPU % usage

			100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

12) Setup Alerts

#### NB! JSON model file -  is a config copy of current working Dashboard.
#### NB! Export Admins rules file - is a config copy of current working Alerts.
