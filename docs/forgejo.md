# Forgejo  
  
## Introduction  
Forgejo is a self hosted version of Github and the project itself is a hard fork of Gitea.
I really wanted to get my feet wet to start working like a DevOps engineer so I decided to self host my own documentation and store my configuration files in my own homelab.  
Since I have deployed Forgejo I am having with migration my repo's from Github and I have created my fictional IT company.
  
## Installation  
You can deploy this application in Portainer, but I decided to setup a dedicated container and go from there.  
After updating the distro, installing the needed applications and Docker (Compose).  
I am going to deploy Forgejo with a Postgres database, but you can choose to leave that part out to use the defalt SQLite database.
  
1. Start by making the directory forgejo.  
2. Create a docker-compose.yml file.  
`````yaml linenums="1" title="Forgejo Config"  
networks:
  forgejo:
    external: false
  
services:  
  server:  
    image: codeberg.org/forgejo/forgejo:11  
    container_name: forgejo  
    environment:  
      - USER_UID=1000  
      - USER_GID=1000  
      - FORGEJO__database__DB_TYPE=postgres  
      - FORGEJO__database__HOST=db:5432  
      - FORGEJO__database__NAME=forgejo  
      - FORGEJO__database__USER=forgejo  
      - FORGEJO__database__PASSWD=SUPERSECRETPASSWORD  
    restart: unless-stopped  
    networks:  
      - forgejo  
    volumes:  
      - ./forgejo:/data  
      - /etc/timezone:/etc/timezone:ro  
      - /etc/localtime:/etc/localtime:ro  
    ports:  
      - '3000:3000'  
      - '222:22'  
    depends_on:  
      - db  

  db:  
    image: postgres:14  
    restart: unless-stopped  
    environment:  
      - POSTGRES_USER=forgejo #Same database user from the environment section  
      - POSTGRES_PASSWORD=SUPERSECRETPASSWORD #Same one from the environment section  
      - POSTGRES_DB=forgejo #Same database from the environment section  
    networks:  
      - forgejo  
    volumes:  
      - ./postgres:/var/lib/postgresql/data  
`````  
3. Run `docker compose up -d`.  
4. When everyhting is running you can navigate to the Forgejo instance: your-ip:3000.  
5. The intial setup can start.  
On the intial conficuration screen you will see values you have inserted, check if they are right.  
If not, there is still a way back if something is not right.  
6. In the general settings screen you can costumize the instance further.  
For instance you can set your own instance titel and slogan which is fun and pay attention to the server domain and Base URL.  
I highly recommend to disable self-registration and Enable update checker option.  
7. Furthermore setup SMTP on the general settings page.  
I just left it for now, but come back at it later when I fugure stuff out myself.  
8. Still on the General settings page the Server and third-party service settings.
In my instance I left this alone.
9. Last, but not least on the general settings page you can setup your administor account.  
I highly suggest to setup a separate administrator account and use a normal account for all your CI/CD stuff.  
10. After going through the setup you will land on the mainpage for the first time!
11. In the top right corner click on the admin profile picture and click on **site adminsitration**.
12. Under **Identity & access -> User accounts** where we are going to create the first user account.  
13. Click on **Create user account** and insert the required fields.  
14. Afterwards you can login with your newly created user account and start using Forgejo.  
You can always to the admin account if you need to change something.
   
## The app.ini  
You might have noticed that you cannot change everything in the **site administration** section.  
That is where the `app.ini` file comes in.  
If you have deployed Forgejo the same way as I did you can SSH to your Forgejo instance.  
Then navigate to **/home/USER/forgejo/forgejo/gitea/conf** .  
There you can edit every setting in the `app.ini` file.  
In my case I needed to change the needed the base URL, because put Forgejo through my reserve proxy.  
I also noticed the disable self registration was not disabled so I disabled it via the app.ini file.

## Migrating repos from Github  
I can speak from experience wiht migrating from Github to Forgejo, but you can migrate from Gitlab, Forgejo, Gitea, Gogs, OneDev, Gitbucker, Codebase.
You can also migrate a repo by using only Git if the support service support it.  
Before beginning the migration I temporaarly set my private to public and as soon as the migration was completed I set those repos back to private.  
**Note:** When setup the repo as a mirror then the repo on Forgejo will be read-only.  
  
When migrating from Github to Forgejo:  
1. Click  on the plus sign and select **New migration**.  
2. Select Github.  
3. Get the clone URL from your Github repo.  
4. Select if you need to the repo to be mirrored and/or migrating large files.  
5. Select an owner.
6. Give the repo a name.
7. Make the repo private if you like to and add a description to your new Forgejo repo.

## Future of my Forgejo instance
I am planning to look into Forgejo runners and Forgejo actions when I build some knowledge about GIT and CI/CD.  
When I setup mail with my own domain I might also setup SMTP in Forgejo.
In time when I discover more about Forgejo I look into more customizing options and ways to make my Forgejo instance more secure like 2FA.  
  
## Resources  
- [Forgejo offical website](https://forgejo.org/)
- [Forgejo docs](https://forgejo.org/docs/v11.0/)
- [Forgejo repo](https://codeberg.org/forgejo)
- [Awesome Open Source tutorial](https://www.youtube.com/watch?v=FPVpKCvFQr8)