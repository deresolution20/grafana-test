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

![Screenshot from 2023-01-30 13-23-42](https://user-images.githubusercontent.com/85902399/215604315-1f093b73-5c9c-4c05-82c1-4fe46202f5bb.png)




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

One issue I ran into today (1/30/2023) when deploying the stack in portainer was a "request failed with status code 500" in portainer for the grafana image. This did not happen the first time I deployed this last week so something has probably changed in my local environment. 

I checked the logs inside portainer for Grafana, but did not see anything really stand out.  I checked the portainer website (https://portal.portainer.io/knowledge/kb-search-results?term=error+code+500) for the issue but really wasn't finding anything concrete so I checked the subreddit for Portainer (http://reddit/r/portainer) and a few people mentioned the issue happens because of a port conflict.

I tried editing the docker compose file first by editing it to use port 3001, but Grafana still wanted to use port 3000. I checked the docker image details in portainer and could see that it was setting port 3000 as the default exposed port.

![Screenshot from 2023-01-30 14-49-35](https://user-images.githubusercontent.com/85902399/215604123-32342614-cfbd-4b91-8fd7-bdc248212746.png)


As time is a factor and I need time to prepare this for presentation, I decided to just change the port in the Grafana image by editing the Grafana defaults.ini file inside the docker image.  I can come back to the port issue later.

<br/>
<br/>

I used Dockers docs and found the command used to run commands inside a container (https://docs.docker.com/engine/reference/commandline/exec/).  With this, I was able to run bash inside the container because I wanted to edit the grafana defaults.ini file and change the port using vi. I ran into another issue where I did not have root access to edit the file, and attempted to install sudo to the image, but that failed. It looks like this is an issue people run into and I was able to find this on Grafana's site (https://community.grafana.com/t/how-to-find-grafana-ini-file-in-docker-container/42898/2) so I re-ran docker exec as root and was able to continue.


![Screenshot from 2023-01-30 11-46-05](https://user-images.githubusercontent.com/85902399/215606431-babd4bfa-2b8e-4fb7-8abb-7b072795bca6.png)


```
sudo docker exec -u root -it 9d63f85d23cf /bin/bash
```


<br/>
<br/>

I edited the default.ini file in the conf directory using vi.

![Screenshot from 2023-01-30 11-41-00](https://user-images.githubusercontent.com/85902399/215607171-e8bcad4e-17f4-4b4c-a2fc-e02405762b19.png)



<br/>
<br/>

![Screenshot from 2023-01-30 11-41-30](https://user-images.githubusercontent.com/85902399/215606846-403c4de8-b26e-4668-b95e-bd5a2cf162bf.png)


<br/>
<br/>

Once I had the file saved and image updated I was able to deploy the container and move on to setting up the rest of the stack.














