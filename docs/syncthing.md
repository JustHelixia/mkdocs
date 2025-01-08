# Syncthing

## Introduction
Syncthing is being used to sync files/folders between devices.  
This is an adjuction of the Obsidian article.  
Just could that is the magic sauce to keep the Obsidian vault synced everywhere!

## Deploying Syncthing
1. Navigate to your Portainer installation
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Syncthing Config"
services:
  syncthing:
    image: lscr.io/linuxserver/syncthing:latest
    container_name: syncthing
    hostname: syncthing #optional
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
    volumes:
      - /path/to/syncthing:/config
      - /path/to/obsidian/vaults:/obsidan
    ports:
      - 8384:8384 #Application WebUI
      - 22000:22000/tcp #Listening port (TCP)
      - 22000:22000/udp #Listening port (UDP)
      - 21027:21027/udp #Protocol discovery
    restart: unless-stopped
````
*Note that the second volume path leads to your Obsidian vault(s).*  
6. Then click on **Deploy stack**  
  
## Setting up Syncthing
1. Navigate to your Syncthing instance  
2. At first login create a new user by navigating to **Actions -> Options -> GUI** and enable the **Use HTTPS for GUI** option
3. Navigate to **Actions -> Options -> Connections** and disable the following:  
    - Global Discovery
    - Enable Relaying
    By disabling these options you make your Syncthing instancce just a bit safer especially if you want to expose Syncthing instance.  
    I would advise against it and instead expose your Obsidian instance to the internet.
4. Install Obsidian + Syncthing client on your Debian/Ubuntu based client computer and perform steps 1 through 3  
5. Set up the connection between the server and the client side  
    1. On the client navigate to **Actions -> Show ID** and copy the Device ID
    2. On the server click on **Add Remote Device**
    3. Paste the Device ID and give your client a name
    4. Within a minute you would see your client computer as **Up to Date**
6. After logging in with the user add your Obsidian vault folder  
    1. Click on **Add Folder**
    2. Insert a Folder Label  
    3. Insert the absolute path to your Obsidian vault  
    4. In the **Sharing** tab select your client computer to share the folder
    5. Winthin a minute you would the shared folder as **Up to Date**  
*If you like you can delete the default shared folder*  
  
## Resources
- [MRP tutorial on Obsidian+Syncthing](https://www.youtube.com/watch?v=KVZmLjt270c)
- [Syncthing Docker container](https://fleet.linuxserver.io/image?name=linuxserver/syncthing)
- [Syncthing container documentation](https://docs.linuxserver.io/images/docker-syncthing/)