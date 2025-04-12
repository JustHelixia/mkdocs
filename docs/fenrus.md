# Fenrus

## Introduction
Fenrus is a pretty simple dashboard application if you are new to Homelab dashboards in general this is an excellent starting point.  
You can list your services on the dashboard and open them by clicking on them.  
And sometimes you can also check the health of your services from the dashboard.  
You can also group your services.  
  
## Deploying the container
1. Navigate to your Portainer instance
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Fenrus Config"
services:
  fenrus:
    image: revenz/fenrus:latest
    container_name: fenrus
    environment:
      - TZ=Europe/Amsterdam
    volumes:
      - /etc/fenrus/data:/app/data
    ports:
      - 3000:3000
    restart: unless-stopped
````
6. Then click on **Deploy stack**  
  
In the settings under dashboard is where all the magic happens.  
By selecting groups you can group your services together.  
When you click on a group you can add your services.  
You can add 4 different types of apps: App, Link, Dashboard and terminal.  
I mainly use the App type, but in cases I cannot then I use the Link types.  
  
## Resources
- [Fenrus Github](https://github.com/revenz/Fenrus)