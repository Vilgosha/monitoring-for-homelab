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

6) Add path for metric storage. (Example: /mnt/data_storage/prometheus_data) 

		mkdir /mnt/data_storage/prometheus_data
	
	6.1) If container always restarts and container logs shows this:

		goroutine 1 [running]: github.com/prometheus/prometheus/promql.NewActiveQueryTracker({0x7fff383bff57, 0xb}, 0x14, {0x44aa9e0, 0xc00062de50}) 
		/app/promql/query_logger.go:123 +0x425 main.main() /app/cmd/prometheus/main.go:748 +0x82fe ts=2025-10-14T07:26:40.389Z caller=main.go:617 level=info msg="Starting 				Prometheus Server" mode=server version="(version=2.52.0, branch=HEAD, 
		revision=879d80922a227c37df502e7315fad8ceb10a986d)" ts=2025-10-14T07:26:40.389Z caller=main.go:622 level=info build_context="(go=go1.22.3, platform=linux/amd64, 				user=root@1b4f4c206e41, date=20240508-21:56:43, tags=netgo,builtinassets,stringlabels)" ts=2025-10-14T07:26:40.389Z caller=main.go:623 level=info host_details="(Linux 			6.14.0-33-generic #33-Ubuntu SMP PREEMPT_DYNAMIC Wed Sep 17 23:22:02 UTC 2025 x86_64 fe95d0a3264c (none))" ts=2025-10-14T07:26:40.389Z caller=main.go:624 level=info 			fd_limits="(soft=1073741816, hard=1073741816)" ts=2025-10-14T07:26:40.390Z caller=main.go:625 level=info vm_limits="(soft=unlimited, hard=unlimited)" ts=202	
		5-10-14T07:26:40.391Z caller=query_logger.go:93 level=error component=activeQueryTracker msg="Error opening query log file" file=/prometheus/queries.active err="open 			/prometheus/queries.active: permission denied" panic: Unable to create mmap-ed active query log

   Solution is to give ownership to the container's Prometheus user (UID 65534):
   
		sudo chown -R 65534:65534 /mnt/data_storage/prometheus_data
	
		sudo chown -R 65534:65534 /mnt/data_storage/prometheus_data/prometheus

	Then:

		docker compose down

		docker compose up -d

8) Start yourn own monitoring system:

	docker compose up -d 

9) Open in browser localhost:3535 you should see there login into Grafana
   
	user: admin
	
	password: (see step 5) 

10) Try dashboard 893 for docker container.

11) Setup dashboards for CPU & RAM usage in Grafana.

	Code for RAM % usage

			(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

	Code for CPU % usage

			100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

12) Setup Alerts

#### NB! JSON model file -  is a config copy of current working Dashboard.
#### NB! Export Admins rules file - is a config copy of current working Alerts.
