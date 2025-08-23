# FreshRSS

## Introduction
A while back I was using a RSS reader and I wanted to find a self hosted solution to get news from websites I am interested in.  
I find it easier to use a RSS reader rather than going to every website or using something like Google news. Oh. There are no cookie walls.  
Besides that you also can add Youtube channels to the feed by using their channels URL, (collections of) Subreddits in the old Reddit layout among other things.  
  
In this deploy the application which connects to Postgres is going to deployed, but MariaDB and MySQL are supported too.  
You also deploy this with Docker Compose without Portainer.  
When you delete the database variables FreshRSS is going to use SQLite.  
You are of course welcome to deploy FreshRSS however you want.  
    
## Deploying
1. Navigate to your Portainer instance
2. Select your environment and navigate to **Stacks**
3. Click on **Add stack**
4. Give the stack a name select the **Web editor** as build method
5. Paste the Docker Compose configuration in the Web editor
```` yaml linenums="1" title="FreshRSS Config"
volumes:
  data:
  extensions:
  db:

services:
  freshrss:
    image: freshrss/freshrss:latest
    container_name: freshrss
    hostname: freshrss
    restart: unless-stopped
    logging:
      options:
        max-size: 10m
    volumes:
      - data:/var/www/FreshRSS/data
      - extensions:/var/www/FreshRSS/extensions
    ports:
      - 8080:80
    environment:
      TZ: Europe/Amsterdam
      CRON_MIN: '3,33'
      TRUSTED_PROXY: 172.16.0.1/12 192.168.0.1/16
      FRESHRSS_INSTALL: |-
        --api-enabled
        --base-url ${BASE_URL}
        --db-base ${DB_BASE}
        --db-host ${DB_HOST}
        --db-password ${DB_PASSWORD}
        --db-type pgsql
        --db-user ${DB_USER}
        --default-user admin
        --language en
      FRESHRSS_USER: |-
        --email ${ADMIN_EMAIL}
        --language en
        --password ${ADMIN_PASSWORD}
        --user admin
      depends_on:
        - freshrss-db

  freshrss-db:
    image: postgres:17
    container_name: freshrss-db
    hostname: freshrss-db
    restart: unless-stopped
    logging:
      options:
        max-size: 10m
    volumes:
      - db:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: ${DB_BASE:-freshrss}
      POSTGRES_USER: ${DB_USER:-freshrss}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-freshrss}
    command:
      # Examples of PostgreSQL tuning.
      # https://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server
      # When in doubt, skip and stick to default PostgreSQL settings.
      - -c
      - shared_buffers=1GB
      - -c
      - work_mem=32MB
````
6. Then click on **Deploy stack**  
  
## Post deploying
First go to your FreshRSS instance and go through the installation process.  
Then login where you see the feeds about FreshRSS.  
You can your add own RSS websites with an OPML file or add your RSS subscription manually.

### Importing your OPML, JSON or ZIP file
1. Click on **Subscription management**
2. Navigate to **Import / export**
3. Then browse for your file and click on **Import**
  
### Adding your feeds manually
Before you add a feed, it is a good idea to add a category.  
  
1. Click on **Subscription management**
2. Navigate to **Add a feed or category**
3. First add a category by inserting a category name
4. And click on **Add**
5. Then you are ready to add a feed by inserting a **Feed URL**
6. Click on **Add** to add the URL to your feed
7. Review the page of the added feed and click on submit
8. Click outside the box to close it

## Third party apps
This part is completely *optional* if you have your FreshRSS behind a reverse proxy you can access that URL from everywhere.  
Before you choose any (mobile) app you need to enable **Allow API access** within FreshRSS.

### Enabling API access
1. In FreshRss click on the setting icon in the right corner
2. Go to **Administratation -> Authentication**
3. There you tick the box **Allow API access** and click on **Submit**
4. Then go to **Profile** to create the API login
5. Create a strong password and insert under **External access via API -> API password** and click on **Submit**
6. Then click on the link after **Check API status via:**
7. On that page you can check your API status for Google Reader and Fever  
You may see the following message: *WARN: Probable invalid base URL in ./data/config.php*. This does not mean the API is broken.
8. To check if the APIs are OK you can do the following
    - Google Reader API: https://freshrss.yourdomain.com/api/greader.php
    When the page say **OK** all is well.
    - Fever API: https://freshrss.yourdomain.com/api/fever.php
    If you see a JSON page with **api_version** shows a version and **auth** show *0* then everyhting is alright.

After the setting up the API access your use your username, API password and API URL to feed your subscription to your preffered apps.  

On mobile I used Feedme, but I ended up liking Readrops more.  
Both apps have the option to select FreshRSS as a service and setting it up is really easy.  
  
On my Linux desktop I use the Newsflash app to read my subscriptions, but I ran into errors on Fedora and I am now reading the feed on my FreshRSS feed instead.
    
## Resources
- [Lawrance's tutorial](https://www.youtube.com/watch?v=wcof-Noho9Q)
- [Techdoxs tutuorial](https://www.youtube.com/watch?v=W0jRuq4v810)
- [SYNACK Time tutorial](https://www.youtube.com/watch?v=4bG3_gM5KF0)
- [FreshRSS website](https://www.freshrss.org/)  
- [FreshRSS Github](https://github.com/FreshRSS/FreshRSS)  
- [Third-party apps for FreshRSS](https://github.com/FreshRSS/FreshRSS/blob/edge/README.md#apis--native-apps)  
- [NewsFlash Flathub](https://flathub.org/apps/io.gitlab.news_flash.NewsFlash)  
- [NewsFlash Gitlab](https://gitlab.com/news-flash/news_flash_gtk) 
- [Readrops PlayStore](https://play.google.com/store/apps/details?id=com.readrops.app&pli=1)  
- [Readrops F-Droid](https://f-droid.org/en/packages/com.readrops.app/)
- [Readrops Github](https://github.com/readrops/Readrops)  