# grafana-test


<h2>Description</h2>

Grafana test deploy with Prometheus using docker containers with exporters: Node exporter, and Cadvisor

<br />

<h2>Languages and Utilities Used</h2>


- Docker and Portainer
  - YAML for the Docker compose file
- Grafana and Prometheusdb


<h2>Environments and tools Used </h2>


- Ubuntu 22.04
- Docker version 20.10.23, build 7155243
- container images: Grafana:latest, prometheus:latest, quay.io/prometheus/node-exporter:latest, gcr.io/cadvisor/cadvisor
- container images were found using the docker hub repo - example: https://hub.docker.com/r/grafana/grafana

<h2>Documentation and learnings Used</h2>

<b>Docker</b>
  - https://hub.docker.com/r/grafana/grafana
  - https://docs.docker.com/storage/volumes/
  - https://docs.docker.com/compose/profiles/
  - Docker course on Kodekloud - https://kodekloud.com/topic/docker-compose/

<b>Portainer</b>
  - https://docs.portainer.io/start/install/server/docker/linux 

<b>Grafana</b>
  - https://grafana.com/docs/grafana/latest/setup-grafana/configure-docker/
  - https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/
  - https://grafana.com/docs/grafana/latest/getting-started/build-first-dashboard/
  - https://grafana.com/docs/grafana/latest/getting-started/get-started-grafana-prometheus/
  - https://grafana.com/grafana/dashboards/1860-node-exporter-full/
  - https://grafana.com/grafana/dashboards/14282-cadvisor-exporter/

<b>Prometheus</b>
  - https://hub.docker.com/r/prom/prometheus
  - https://logz.io/blog/prometheus-tutorial-docker-monitoring/#systemdocker 
<br/>
<br/>


<h2>Program walk-through:</h2>



<b>Step 1</b>:  
  
As I have been learning Docker for the past month and already have it installed locally, I decided to try building a docker stack using pre-built images with docker-compose and I've been wanting to test out Portainer as a GUI for container management so I decided to set that up as well.  I like the idea of using containers for test labs and will be utilizing this tool a lot more in the future.  One potential downside is persistent logs after the container is destroyed.  I need to set that up after reading docs, but a quick googlefu shows that one possibility is adding to the docker run command:
```
grafana-storage:/var/lib/grafana -p 3000:3000 grafana/grafana
```
edit: found the docs for adding it to the compose file, so if I get some extra time, I'll try to test this out.


![Alt text](../../../Pictures/Screenshots/Screenshot%20from%202023-01-30%2013-23-42.png)


  https://grafana.com/docs/grafana/latest/setup-grafana/configure-docker/


Anyways, I want to move on and get the stack deployed so I started by creating the portainer container using documentation from portainer:
  https://docs.portainer.io/start/install/server/docker/linux
  
```  
docker volume create portainer_data

docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```


once that was complete, I logged in using web browser at http://localhost:9000 and created the login credentials



<b>Step 2:</b>

I created the stack by building a docker compose file and uploading it to Portainer and then deployed the stack.
The docker-compose.yml file includes containers for both grafana and prometheus as well as two exporters to pull data in order to build a dashboard in Grafana.  I chose node exporter to log my machine metrics, and cadvisor to log the container metrics. 

See docker-compose.yml file.



![portainer-deploy](https://user-images.githubusercontent.com/85902399/213945441-9d084b3c-7aa4-4b23-9e36-f52d2a3f063b.png)


<br/>
<br/>



![portainer-stack](https://user-images.githubusercontent.com/85902399/213945601-ba5b04bf-17e7-4da8-a223-7cb69dddc820.png)


<br/>
<br/>


<b>Step 3:</b> with the stack deployed, I logged into Prometheus at http://localhost:9090 and verified the export targets were up and there were no errors.

<br/>
<br/>


![prometheus-targets](https://user-images.githubusercontent.com/85902399/213945703-12dddf0e-6bc7-4568-ad7b-9a9b3c3902c7.png)

<br/>
<br/>



<b>Step 4:</b> with the exporters working, I logged into Grafana at http://0.0.0.0:3001/ and added the Prometheusdb as a database source.

<br/>
<br/>


![grafana datasource-setup](https://user-images.githubusercontent.com/85902399/213945924-94981252-681b-4d42-8aa0-ca9ea9f65c25.png)

<br/>
<br/>

<b>Step 5:</b> with the datasource set, I went to Grafana's website and grabbed some pre-built dashboards for node exporter and cadvisor

https://grafana.com/grafana/dashboards/1860-node-exporter-full/

<br/>
<br/>

https://grafana.com/grafana/dashboards/14282-cadvisor-exporter/

I then imported those dashboards using the import function on Grafana.  Cadvisor is pulling data on the containers

<br/>
<b>Cadvisor dashboard</b>
<br/>
<br/>


![cadvisor-dashboard](https://user-images.githubusercontent.com/85902399/213946040-827221cf-227c-4e6e-87e0-034282c62106.png)

<br/>
<br/>

<b>Node exporter dashboard</b>

<br/>
<br/>


![node-explorer-dashboard](https://user-images.githubusercontent.com/85902399/213946083-180ff8da-7454-4e65-b1c6-8f7296830706.png)



<h2> Issues I ran into</h2>

<br/>
<br/>

One issue I ran into when deploying the stack in portainer was a "request failed with status code 500" in portainer for the grafana image.  

I checked the logs inside portainer for Grafana, but did not see anything really stand out.  I checked the portainer website (https://portal.portainer.io/knowledge/kb-search-results?term=error+code+500) for the issue but really wasn't finding anything concrete so I checked the subreddit for Portainer (http://reddit/r/portainer) and a few people mentioned the issue happens because of a port conflict.

I tried editing the docker compose file first by editing it to use port 3001, but Grafana still wanted to use port 3000. 


As time is a factor and I need time to prepare this for presentation, I decided to just change the port in the Grafana image by editing the defaults.ini file














