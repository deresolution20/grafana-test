# grafana-test


<h2>Description</h2>

grafana test deploy with prometheus using docker containers 





<br />





<h2>Languages and Utilities Used</h2>



- <b>Docker and Portainer</b> 

- <b>Grafana and Prometheusdb </b>



<h2>Environments Used </h2>



- <b></b>

- <b>Ubuntu 22</b>



<h2>Program walk-through:</h2>



First step:  I already had Docker installed and Docker compose, so I setup portainer


docker volume create portainer_data

docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer



once that was complete, I logged in using web browser at http://localhost:9000 and created the login credentials



Step 2:

I created the stack by building a docker compose file and uploading it to Portainer and then deploying the stack.
The docker-compose.yml file includes containers for both grafana and prometheus as well as two exporters to pull data in order to build a dashboard in Grafana.  See docker-compose.yml file.



![portainer-deploy](https://user-images.githubusercontent.com/85902399/213945441-9d084b3c-7aa4-4b23-9e36-f52d2a3f063b.png)


<br/>
<br/>



![portainer-stack](https://user-images.githubusercontent.com/85902399/213945601-ba5b04bf-17e7-4da8-a223-7cb69dddc820.png)




Step 3: with the stack deployed, I logged into Prometheus at http://localhost:9090 and verified the export targets were up and there were no errors.

![prometheus-targets](https://user-images.githubusercontent.com/85902399/213945703-12dddf0e-6bc7-4568-ad7b-9a9b3c3902c7.png)









<br />

<br />







