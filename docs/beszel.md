# Beszel

## Introduction
Beszel is a very simple and lightweight tool to monitor your servers and Docker containers.  
While it is a lightweight and simple application it has beautful interface (in my opinion).  
  
## Install the Beszel server
The application will be deployed in Portainer.  
  
1. Navigate to your Portainer instance
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Beszel server Config"
services:
  beszel:
    image: 'henrygd/beszel:latest'
    container_name: 'beszel'
    restart: unless-stopped
    ports:
      - '8090:8090'
    volumes:
      - ./beszel_data:/beszel_data
````
  
6. Then click on **Deploy stack**

## Adding your servers to Beszel server
It is a very simple process by installing the very small Beszel agent on your target server(s).  
I used SSH to install Beszel agent on my server(s), because I have trouble on some servers by doing this in Proxmox.  

### Beszel web interface  
1. Navigate to your Bezsel installation
2. Click on **Add System** in the right corner
3. Insert the name of the system in the **Name**
4. Add the Host / IP in the **Host / IP** field
5. When needed change the port if the noted port is already in use or if you want to use another

### Target system
For these steps you need to install Docker and Docker Compose.  
  
1. Connect to the target system
2. Create a Beszel directory for the Beszel agent `mkdir beszel`
3. Create a docker-compose.yaml file `vim compose-compose.yaml`  
(Or use any other terminal text editor like nano)  
4. Copy the Docker Compose config from the Beszel GUI by clicking **Copy docker compose**
5. Paste the Docker Compose config in your terminal ans save it
6. Run the Beszel agent by running `docker compose up -d`
7. Now wait unitl the system appears online in the Beszel GUI (that takes just a few moments)
  
## Updating
You can redeploy the Beszel container image to update Beszel.
You can follow the steps on the Docker page to redeploy a Docker image to update your Beszel agents.

## Resources
- [Beszel website](https://beszel.dev/)
- [Beszel Github](https://github.com/henrygd/beszel)
- [DB Tech tutorial](https://www.youtube.com/watch?v=Bz7gPYBH0AU)
- [Techdox tutorial](https://www.youtube.com/watch?v=fTpGa4UH6lA)