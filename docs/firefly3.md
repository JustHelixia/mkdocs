# Firefly III

## Introduction
Firefly III is a financial management application.  
It has many features so at times which is way too much to get into in this article.  
  
## Deploying Firefly III
1. Navigate to your Portainer instance
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="Firefly III Config"
#
# The Firefly III Data Importer will ask you for the Firefly III URL and a "Client ID".
# You can generate the Client ID at http://localhost/profile (after registering)
# The Firefly III URL is: http://app:8080
#
# Other URL's will give 500 | Server Error
#

services:
  app:
    image: fireflyiii/core:latest
    hostname: app
    container_name: firefly_iii_core
    networks:
      - firefly_iii
    restart: always
    volumes:
      - firefly_iii_upload:/var/www/html/storage/upload
    env_file: stack.env
    ports:
      - '8059:8080'
    depends_on:
      - db
  db:
    image: mariadb:lts
    hostname: db
    container_name: firefly_iii_db
    networks:
      - firefly_iii
    restart: always
    env_file: stack.env
    volumes:
      - firefly_iii_db:/var/lib/mysql

  importer:
    image: fireflyiii/data-importer:latest
    hostname: importer
    restart: always
    container_name: firefly_iii_importer
    networks:
      - firefly_iii
    ports:
      - '85:8080'
    depends_on:
      - app
    env_file: stack.env

  cron:
    #
    # To make this work, set STATIC_CRON_TOKEN in your .env file or as an environment variable and replace REPLACEME below
    # The STATIC_CRON_TOKEN must be *exactly* 32 characters long
    #
    image: alpine:latest
    container_name: firefly_iii_cron
    restart: always
    command: sh -c "echo \"0 3 * * * wget -qO- http://app:8080/api/v1/cron/REPLACEME;echo\" | crontab - && crond -f -L /dev/stdout"
    networks:
      - firefly_iii
volumes:
   firefly_iii_upload:
   firefly_iii_db:

networks:
  firefly_iii:
    driver: bridge
````  
 6. Then click on **Deploy stack**  
  
## Resources
- [Firefly III website](https://www.firefly-iii.org/)
- [Firefly III Github](https://github.com/firefly-iii/firefly-iii)
- [Firefly III documentation](https://docs.firefly-iii.org/)
- [DB Techs tutorial](https://www.youtube.com/watch?v=8vsLx1losCE)
- [Techdoxs tutorial](https://www.youtube.com/watch?v=w_wdA60pePc)