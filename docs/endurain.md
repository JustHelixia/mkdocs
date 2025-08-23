# Endurain  
  
## Introduction  
Workouts and nerd do not blend together. Right?  
Most of the time they do not, but there are a few nerds who like to run.  
I know a few of them myself included.  
  
I also wanted to a way to track my walks and runs in my homelab.  
After searching a bit I found Endurain which is essentially a self hosted version of Strava.  
If you already use Strava you can also connect your Strava account to Endurain.  
  
I must confess I struggled quite a bit with this deploy, because it was my first time figuring out creating API-users to connect 2 applications.  
    
## Preparations  
  
First create another user than root if you haven't already.  
  
`````bash title="Endurain Preparation"  
sudo adduser USERNAME  
cat /etc/group  
sudo adduser USERNAME sudo  
groups #check the result of the last command
`````  
  
Secondly update your container/VM and install the needed software.  
  
`````bash title="Updating & installing apps"  
sudo apt update && sudo apt upgrade -y  
sudo apt install curl -y  
curl -fsSL https://get.docker.com -o get-docker.sh  
sh get-docker.sh  
sudo adduser USERNAME docker  
groups  #check if you are part of the Docker group
docker run hello-world  
`````  
  
## Installing Endurain  
  
After the preparations it is time to install Endurain.  
During this process you will create the needed directory structure and downloading the example Docker Compose files.  
  
`````bash title="Installing Endurain"  
sudo mkdir /opt/endurain  
id #checking your id which is needed for chown
sudo chown 1000:1000 /opt/endurain  
mkdir -p \  
  /opt/endurain/backend/{user_images,server_images,files,logs} \  
  /opt/endurain/postgres  
  
cd /opt/endurain  
wget https://raw.githubusercontent.com/joaovitoriasilva/endurain/refs/heads/master/docker-compose.yml.example  
wget https://raw.githubusercontent.com/joaovitoriasilva/endurain/refs/heads/master/.env.example  
  
mv docker-compose.yml.example docker-compose.yml  
mv .env.example .env  
  
vim .env #Change the changme values in .env  
`````  
  
### Example .env file  
  
The values in the .env file are still on their defaults.  
Of course for security reasons you will need to change the credentials and set the values to match your environment.
After editing the .env file you are reading to bring the container up and check the logs if all goes well.
  
`````yaml linenums="1" title="Editing the .env file"  
# This is an environment variable file for Endurain's docker-compose.yml.example  
# These are just the variable you have to set to be up and running.  
# There is many more variable you could set. Check them out here: https://docs.endurain.com/getting-started/advanced-started/#supported-environment-variables  
  
DB_PASSWORD=changeme # Create with `openssl rand -hex 32`  
POSTGRES_PASSWORD=sama as DB_PASSWORD  
SECRET_KEY=changeme # Create with `openssl rand -hex 32`  
FERNET_KEY=changeme # Go to https://fernetkeygen.com/ for a key  
GEOCODES_MAPS_API=changeme # Create an account at https://geocode.maps.co/join/ or log in if you have an account https://geocode.maps.co/login/  
TZ=Europe/Lisbon  
ENDURAIN_HOST=https://endurain.example.com # The domain are you going to use  
BEHIND_PROXY=true # If your Endurain is behind a reverse proxy  
POSTGRES_DB=endurain  
POSTGRES_USER=endurain  
PGDATA=/var/lib/postgresql/data/pgdata  
`````  
Source - https://docs.endurain.com/getting-started/getting-started/#docker-compose-deployment  
  
`````bash  
sudo docker compose up -d  
sudo docker compose logs -f  
  
ip a |  grep xxx.xxx.xxx # fill in the parts of the IP-address which you know of your network  
`````  
  
When the Docker containers are up and running (no pun intended) without errors you can add the Endurain to your reserve proxy configuration (if you have set up a reserve proxy).  
Afterwards you are now ready to navigate to your Endurain instance for the first time!  
  
Navigate to endurain.example.com and login with the default values first.  
login with admin - admin  
Logged in, change the admin password someting stronger and create your own account.  

1. After logging in, navigate to the top and click on the gear icon.  
2. Navigate to **security** to change the password of the admin account.  
3. Then navigate to **Users** to create your own user account.  
4. Click on **Add User**.  
5. Insert the needed information of the user (Username, Name, Email, Password).  
6. After clicking on **Add user**, the user can modify their details however they like.  
  
## Strava  
  
### Creating your API-application  
1. Login to Strava  
2. Go to https://www.strava.com/settings/api and create an API for your application.  
   Required information:  
   - Application Name  
   - Category  
   - Club: Not required, but you can select it if you like  
   - Website: Your Endurain instance  
   - Application Description  
   - Authorization Callback Domain: the root domain of your Endurain website  
   - An application image is also required  
     
   Explanation of the values in the API application screen:  
	- Category: The category you chose for your application  
	- Club: Will show if you have a club associated with your application  
	- Client ID: Your application ID  
	- Client Secret: Your client secret (please keep this confidential)  
	- Authorization token: Your authorization token which will change every six hours (please keep this confidential)  
	- Your Refresh token: The token you will use to get a new authorization token (please keep this confidential)  
	- Rate limits: Your current rate limit  
	- Authorization Callback Domain: When building your app, change “Authorization Callback Domain” to localhost or any domain. When taking your app live, change “Authorization Callback Domain” to a real domain  
  
### Testing your API  
  
1. Make a curl command on the Endurain host:  
````  
curl -X GET \  
https://www.strava.com/api/v3/athlete \  
-H 'Authorization: Bearer YOURACCESSTOKEN'  
````  
After this you should see your Athlete information from Strava with the API-token you created.  
2. Edit this URL - http://www.strava.com/oauth/authorize?client_id=[REPLACE_WITH_YOUR_CLIENT_ID]&response_type=code&redirect_uri=http://localhost/exchange_token&approval_prompt=force&scope=read  
3. Open a browser and paste the edited URL  
4. Hit enter  
5. When you see the authorization page, click **Authorize**  
6. Copy the authorization code  
7. Make a CURL request to exchange the authorization code and scope for a refresh token, access token, and access token expiration date  
Replace the client_secret and code. The response should include the refresh token, access token, and access token expiration date  
8. curl -X POST https://www.strava.com/oauth/token \  
	-F client_id=YOURCLIENTID \  
	-F client_secret=YOURCLIENTSECRET \  
	-F code=AUTHORIZATIONCODE \  
	-F grant_type=authorization_code  
9.  If all the steps give results from the API then everything is working correctly  
  
### Connecting Strava to Endurain  
1. Go back to Endurain  
2. Click the settings gear and navigate to integrations  
3. Click on connect next to Strava  
4. Insert your Cliend ID and Client Secret  
5. Authorize Endurain  
6. Under options you can optionally retrieve activities by days and retrieve gear, but when you connect the first time it should import those things automatically   
  
After this challenging deploy you are now ready challenge yourself during the workouts.  
  
## Resources  
- [Endurain documentation](https://docs.endurain.com/)  
- [Endurain Github](https://github.com/joaovitoriasilva/endurain)  
- [Strava API setup guide](https://developers.strava.com/docs/getting-started/)