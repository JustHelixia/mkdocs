# Nginx Proxy Manager

## Introduction
Nginx Proxy Manager (NPM) is a reverse proxy maanger you can use this to replace IP-addresses with domainnames.  
You can use .local or get a free domain name with a service like [duckdns](https://www.duckdns.org/). 
For example you can use a domainname for your Portainer installation.  
  
Within NPM you are also secure your websites by using SSL-certificates from letsencrypt and doing a DNS on your recently aquired domain.  
  
## Installing
I am using Portainer and Docker compose to install NPM.
(I might move this container to a LXC container on my Proxmox server.)

1. Navigate to your Portainer installation
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="NPM Config"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP
    environment:
      # Mysql/Maria connection parameters:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm-user"
      DB_MYSQL_PASSWORD: "FancyPassword"
      DB_MYSQL_NAME: "npm"
      # Uncomment this if IPv6 is not enabled on your host
      DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 'npm'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm-user'
      MYSQL_PASSWORD: 'FancyPassword'
      MARIADB_AUTO_UPGRADE: '1'
    volumes:
      - ./mysql:/var/lib/mysql
````

 6. Then click on **Deploy stack**  
   
 ## Rescources  
- [NPM website](https://nginxproxymanager.com/)  
- [NPM documentation](https://nginxproxymanager.com/guide/)  
- [NPM Github](https://github.com/NginxProxyManager/nginx-proxy-manager)  
- [Christians tutorial](https://www.youtube.com/watch?v=P3imFC7GSr0)  
- [Wolfgang's tutorial](https://www.youtube.com/watch?v=qlcVx-k-02E)  