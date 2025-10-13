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

user = example@gmail.com

Do not use your main email address!

I would recommend to use App password from google!

Password must be without spaces!

password = App_password

from_name = example@gmail.com

4) Check prometheus.yml if any changes required for your setup.

5) Change in docker-compose.yml GF_SECURITY_ADMIN_PASSWORD 

6) Start yourn own monitoring system:

	docker compose up -d 

7) Open in browser localhost:3535 you should see there login into Grafana
	user: admin
	password: (see step 5) 

8) Try dashboard 893 for docker container.

9) Setup dashboards for CPU & RAM usage in Grafana.

10) Setup Alerts.

