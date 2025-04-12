# Wallos

## Introduction
If you like to track your subscriptions.  
Then you should take a look at Wallos.  
With Wallos you can track the costs, experiation date and more.  

## Installation
I am using Docker Compose and Portainer to install Wallos.  
  
1. Open Portainer
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Wallos Config"
services:
  wallos:
    container_name: wallos                          # Names the container for easier management.
    image: bellamy/wallos:latest                    # Specifies the Wallos Docker image.
    ports:
      - "8282:80/tcp"                               # Exposes Wallos on port 8282.
    environment:
      TZ: 'Europe/Amsterdam'                         # Sets the timezone for the container.
    volumes:
      - './db:/var/www/html/db'                     # Stores Wallos' database for persistent data.
      - './logos:/var/www/html/images/uploads/logos' # Stores logos uploaded to the app.
    restart: unless-stopped                         # Ensures the container restarts automatically unless manually stopped.
````
6. Then click on **Deploy stack**
 
After deploying Wallos you can access Wallos on http://SERVERIP:8282.  
  
## Resources:
- [Wallos website](https://www.wallosapp.com/)
- [Wallos Github](https://github.com/ellite/Wallos)
- [DB Tech tuurial](https://www.youtube.com/watch?v=gqqk8WTOZ3Q&t=424s)
- [Techdox tutorial](https://www.youtube.com/watch?v=Qq6pCwjnNM8)