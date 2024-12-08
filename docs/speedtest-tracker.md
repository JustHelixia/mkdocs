# Speedtest Tracker

## Introduction
I was looking up Docker container to deploy first as my very first Docker container.  
It took me a few tries to get right and spend much time customizing it.
In Portainer I added the Speedtest-tracker software stack as a Docker Compose file.  

``` yaml linenums="1"
services: 
  speedtest-tracker:
    container_name: speedtest-tracker
    image: "linuxserver/speedtest-tracker:latest" # Pulls latest image.
    ports:
      - "8888:80"
      - "8443:443"
    environment:
      - APP_KEY=  #The secret key for the application Go to https://speedtest-tracker.dev/ and generate a new key
      - PUID=1000
      - PGID=1000
      - DB_CONNECTION=mysql
      - DB_HOST=db
      - DB_PORT=3306
      - DB_DATABASE=speedtest_tracker
      - DB_USERNAME=speedy
      - DB_PASSWORD=NicePasswordHere
      - DISPLAY_TIMEZONE=Europe/Amsterdam
      - CHART_DATETIME_FORMAT=d-m-o, H:i # day of the month-month-full year, 24-hours display with leading zeros, minutes with leading zeros (https://www.php.net/manual/en/datetime.format.php)
      - DATETIME_FORMAT=l j F o, H:i # weekday, day of the month, fully wriiten month year, 24-hours display with leading zeros, minutes with leading zeros (https://www.php.net/manual/en/datetime.format.php)
      - SPEEDTEST_SCHEDULE="0 * * * *" # Every hour, every day (https://it-tools.tech/crontab-generator)
      - PRUNE_RESULTS_OLDER_THAN=14
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "speedtest_tracker_config_data:/config"
      - "speedtest_tracker_web_data:/etc/ssl/web"
    restart: unless-stopped
    depends_on:
      - db

  # Service: db (Database)
  db:
    image: mariadb:10
    restart: always
    environment:
      - MARIADB_DATABASE=speedtest_tracker
      - MARIADB_USER=speedy
      - MARIADB_PASSWORD=NicePasswordHere
      - MARIADB_RANDOM_ROOT_PASSWORD=true
    volumes:
      - speedtest_tracker_db_data:/var/lib/mysql

volumes:
  speedtest_tracker_config_data:
    name: speedtest_tracker_config_data
    driver: local
  speedtest_tracker_web_data:
    name: speedtest_tracker_web_data
    driver: local
  speedtest_tracker_db_data:
    name: speedtest_tracker_db_data
    driver: local
```