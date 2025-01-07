# Obsidian

## Introduction
I already was a fan of Obsidian and I saw I could deploy it as a Docker container!  
For me that sounded really exciting!  
I also wanted a way to work from any device on some files in my Obsidian vault.  
For the mentioned above you will setup something like Syncthing.  
I will describe how to setup Syncthing in another article on the website.  
Letś go and deploy Obsidian!  
  
## Deploying
Obsidian is being hosted in a Kasm workplace Docker container.  
Kasm is an application which let's you run 1 or more application in a Docker container.  
In this article I am going to deploy this Docker container in Portainer with Docker Compose.  
  
1. Navigate to your Portainer installation
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Obsidian Config"
services:
  obsidian:
    image: lscr.io/linuxserver/obsidian:latest
    container_name: obsidian
    security_opt:
      - seccomp:unconfined #optional
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
      - CUSTOM_USER=USER
      - PASSWORD=FancyPasswordForUSER
    volumes:
      - /home/helixia/obsidian:/config
    ports:
      - 3030:3000
      - 3101:3001
    devices:
      - /dev/dri:/dev/dri #optional
    shm_size: "1gb"
    restart: unless-stopped
````
*The volume value will become important in the Syncthing atricle.*  
6. Then click on **Deploy stack**  
7. The Docker container comes with a default username abc (with password abc) and you might wanne disable the account or change its password.  
Disabling the account: `usermod -L -e 1 abc`  
Changing the password: swith to the abs user and change the password.  
  
## Environment values
If you are as new as me to Docker then you might notice some values which are unknow to you.  
Below I will describe these values.  
- **security_opt: - seccomp: unconfined** - If you are running on an older host system you need to enable this option to run modern GUI apps properly. I have set this option just in case although I run a modern mini-PC.
- **volumes:** - Remember that value to setup syncing.
- **devices: - /dev/dri:/dev/dri** - This will enable DRI3 GPU acceleration. Intel, AMD and Nvidia GPUs are supported.
- **shm_size: "1gb"** - This is needed for Obsidian to launch properly, because it is an Electron application.
  
## Resources
- [MRP tutorial on Obsidian+Syncthing](https://www.youtube.com/watch?v=KVZmLjt270c)
- [Obsidian Docker container](https://fleet.linuxserver.io/image?name=linuxserver/obsidian)
- [Obsidian container documentation](https://docs.linuxserver.io/images/docker-obsidian/)