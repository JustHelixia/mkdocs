# Homepage

## Introduction
Homepage is a dashboard for your homelab just like Fenrus.  
The big difference is that configuring the dashboard is a bit more difficult, because you don't use a GUI instead you are using yaml files.  

## Deploying Homepage
This time I decided to deploye this Docker container a little bit different.  
I SSHed to my Portainer VM and created the directory and files for the Docker container.  
  
**Steps:**
1. I SSHed to my Portainer VM
2. Created the Homepage folder with the Docker Compose file
```` yaml linenums="1" title="Homepage Config"
services:
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
      - CONTAINERS=1 # Allow access to viewing containers
      - SERVICES=1 # Allow access to viewing services (necessary when using Docker Swarm)
      - TASKS=1 # Allow access to viewing tasks (necessary when using Docker Swarm)
      - POST=0 # Disallow any POST operations (effectively read-only)
    ports:
      - 127.0.0.1:2375:2375
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro # Mounted as read-only
    restart: unless-stopped

  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    env_file: .env
    environment:
      - PUID=$PUID
      - PGID=$PGID
      - HOMEPAGE_VAR_PROXMOX_URL=$HOMEPAGE_VAR_PROXMOX_URL
      - HOMEPAGE_VAR_PROXMOX_USERNAME=$HOMEPAGE_VAR_PROXMOX_USERNAME
      - HOMEPAGE_VAR_PROXMOX_PASSWORD=$HOMEPAGE_VAR_PROXMOX_PASSWORD
      - HOMEPAGE_VAR_PORTAINER_URL=$HOMEPAGE_VAR_PORTAINER_URL
      - HOMEPAGE_VAR_PORTAINER_API=$HOMEPAGE_VAR_PORTAINER_API
      - HOMEPAGE_VAR_FRESHRSS_URL=$HOMEPAGE_VAR_FRESHRSS_URL
      - HOMEPAGE_VAR_FRESHRSS_MONITOR=$HOMEPAGE_VAR_FRESHRSS_MONITOR
      - HOMEPAGE_VAR_FRESHRSS_USERNAME=$HOMEPAGE_VAR_FRESHRSS_USERNAME
      - HOMEPAGE_VAR_FRESHRSS_PASSWORD=$HOMEPAGE_VAR_FRESHRSS_PASSWORD
      - HOMEPAGE_VAR_BESZEL_URL=$HOMEPAGE_VAR_BESZEL_URL
      - HOMEPAGE_VAR_BESZEL_USERNAME=$HOMEPAGE_VAR_BESZEL_USERNAME
      - HOMEPAGE_VAR_BESZEL_PASSWORD=$HOMEPAGE_VAR_BESZEL_PASSWORD
      - HOMEPAGE_VAR_UPTIMEKUMA_URL=$HOMEPAGE_VAR_UPTIMEKUMA_URL
      - HOMEPAGE_VAR_FRESHRSS_MONITOR=$HOMEPAGE_VAR_FRESHRSS_MONITOR
      - HOMEPAGE_VAR_UPTIMEKUMA_SLUG=$HOMEPAGE_VAR_UPTIMEKUMA_SLUG
      - HOMEPAGE_VAR_GOTIFY_URL=$HOMEPAGE_VAR_GOTIFY_URL
      - HOMEPAGE_VAR_GOTIFY_MONITOR=$HOMEPAGE_VAR_GOTIFY_MONITOR
      - HOMEPAGE_VAR_GOTIFY_KEY=$HOMEPAGE_VAR_GOTIFY_KEY
      - HOMEPAGE_VAR_MKDOCS_URL=$HOMEPAGE_VAR_MKDOCS_URL
      - HOMEPAGE_VAR_MKDOCS_MONITOR=$HOMEPAGE_VAR_MKDOCS_MONITOR
      - HOMEPAGE_VAR_NPM_MONITOR=$HOMEPAGE_VAR_NPM_MONITOR
      - HOMEPAGE_VAR_UNIPRIX_URL=$HOMEPAGE_VAR_UNIPRIX_URL
      - HOMEPAGE_VAR_JENKINS_LOGIN=$HOMEPAGE_VAR_JENKINS_LOGIN
      - HOMEPAGE_VAR_JENKINS_URL=$HOMEPAGE_VAR_JENKINS_URL
      - HOMEPAGE_VAR_SPEEDTESTTRACKER_URL=$HOMEPAGE_VAR_SPEEDTESTTRACKER_URL
      - HOMEPAGE_VAR_SYNCTHING_URL=$HOMEPAGE_VAR_SYNCTHING_URL
      - HOMEPAGE_VAR_FIREFLY_URL=$HOMEPAGE_VAR_FIREFLY_URL
      - HOMEPAGE_VAR_OBSIDIAN_URL=$HOMEPAGE_VAR_OBSIDIAN_URL
      - HOMEPAGE_VAR_WALLOS_URL=$HOMEPAGE_VAR_WALLOS_URL
      - HOMEPAGE_VAR_OPENWEBUI_URL=$HOMEPAGE_VAR_OPENWEBUI_URL
    ports:
      - 3008:3000
    volumes:
      - /home/helixia/homepage/config:/app/config # Make sure your local config directory exists
      - /var/run/docker.sock:/var/run/docker.sock:ro # optional, for docker integrations
    restart: unless-stopped
````
You might notice something interesting. In this project I used a Docker environment files for the first tinme!  
3. When starting the Docker container `docker compose up -d` the needed files are being created in the config folder
4. The most important files are service.yaml, settings.yaml and bookmark.yaml
5. After changing one of the files just reload the page to apply the change