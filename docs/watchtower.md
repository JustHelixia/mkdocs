# Watchtower

## Introduction
Watchtower is an application which you can use to monitor new image releases and to restart container when they are updated.  
I only enabled the notification capability hence I have commented out many options in the example Docker Compose file.  
I am planning to use another application to automate updating Docker containers.  
In my Docker Compose I have added my Gotify notifaction to get mobile notifications about Docker image updates.  
  
## Deploying
1. Navigate to your Portainer instance
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Watchtower Config"
services:
  watchtower:
    image: "containrrr/watchtower:latest"
    restart: unless-stopped
    environment:
      - TZ=Europe/Amsterdam
      # remove old images after update (useful for saving space)
      - WATCHTOWER_CLEANUP=false
      # the below will ignore labels set. It is worth checking out labels as that can be a more scalabe solution (automatic)
      # - WATCHTOWER_DISABLE_CONTAINERS=traefik crowdsec bouncer-traefik deconz frigate home-assistant homeassistant-db
      # the docker host can also be remote by specifying tcp
      #- DOCKER_HOST=tcp://192.168.1.229:8006
      # how frequently to check for images (default is 24 hours)
      # - WATCHTOWER_POLL_INTERVAL=86400
      # choose whether to restart the containers after updates
      - WATCHTOWER_INCLUDE_RESTARTING=true
      # choose whether to update stopped and exited containers
      - WATCHTOWER_INCLUDE_STOPPED=false
      # this will start containers that were stopped or exited if they are updated
      - WATCHTOWER_REVIVE_STOPPED=true
      # Watchtower only notifies
      - WATCHTOWER_MONITOR_ONLY=true
      # you can tell watchtower to do updates and restarts one by one - can be helpful
      # - WATCHTOWER_ROLLING_RESTART=true #

      # Gotify notifications
      - WATCHTOWER_NOTIFICATIONS=gotify
      - WATCHTOWER_NOTIFICATION_GOTIFY_URL=https://gotify.yourdomain.com
      - WATCHTOWER_NOTIFICATION_GOTIFY_TOKEN=GENERATED GOTIFY TOKEN
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
````
6. Then click on **Deploy stack**  
  
## Resources  
- [Jims tutorial](https://www.youtube.com/watch?v=mSSlrRgSAP4)  
- [Techdoxs tutorial](https://www.youtube.com/watch?v=DNfMuDLDq7k)  
- [Watchtower website](https://containrrr.dev/watchtower/) 