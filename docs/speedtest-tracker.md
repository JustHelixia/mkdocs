# Speedtest Tracker

## Introduction
I was looking up Docker container to deploy first as my very first Docker container.  
First I tried MySpeed which was a simple deploy, but I wanted more information about the health of my internet connection.  
So I choose Speedtest Tracker
It took me a few tries to get right and spend much time customizing it.
In Portainer I added the Speedtest-tracker software stack as a Docker Compose file.  

``` yaml linenums="1" title="Speedtest Tracker config"
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
      - CHART_DATETIME_FORMAT=d-m-o, H:i
      - DATETIME_FORMAT=l j F o, H:i
      - SPEEDTEST_SCHEDULE="0 * * * *"
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

## Custom environment settings
- `DISPLAY_TIMEZONE` - To make sure the right time is displayed on my internet speed tests
- `CHART_DATETIME_FORMAT=d-m-o, H:i` - day of the month-month-full year, 24-hours display with leading zeros, minutes with leading zeros
- `DATETIME_FORMAT=l j F o, H:i` - weekday, day of the month, fully wriiten month year, 24-hours display with leading zeros, minutes with leading zeros
- `SPEEDTEST_SCHEDULE="0 * * * *"` - Everyday and every hour Speedtest Tracker does a speedtest.
- `PRUNE_RESULTS_OLDER_THAN=14` - Results older than 14 days are being removed 

## Used resouces
- [DB techs YT video](https://www.youtube.com/watch?v=feArak6WCLw)
- [Speedtest tracker docs](https://docs.speedtest-tracker.dev/)
- [Speedtest-tracker GitHub](https://github.com/alexjustesen/speedtest-tracker)
- [DateTime formatting](https://www.php.net/manual/en/datetime.format.php)
- [Cron job generator](https://it-tools.tech/crontab-generator)