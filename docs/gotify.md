# Gotify

## Introduction
Gotify is a self-hosted application to receive notification when if something is wrong with your homelab.  
They have many intergrations to receive notifications from.  
  
I have plugged in the following applications:  
- Uptime Kuma  
- Watchtower  
- Speedtest-tracker  
- Wallos  
- Beszel  

## Docker Deployment
This is just a very simple Docker Compose file to get it running.  
If you like to receive notifications when you are away from home you will configure Nginx Proxy Manager (treafik or something else) and set the DNS to point to your Gotify site.  
You also need to enable websocket support to let Gotify work properly.  

``` yaml linenums="1" title="Speedtest Tracker config"
services:
    server:
        ports:
            - 8080:80
        environment:
            - TZ=Europe/Amsterdam
        volumes:
            - /var/gotify/data:/app/data
        image: "gotify/server:latest"
```

## Adding services
After initially logging in to your Gotify instance navigate to the top to **APPS**.
On the Applications page click on **create application** to add an Name, Short Description and the priority and create a token.

### Uptime Kuma
1. Login to your Uptime Kuma instance
2. Click on your profile -> Settings
3. Select **Notifications**
4. Click on **Setup Notifications**
5. Select Gotify as **Notification Type** and give the nice name to Gotify notifications system
6. Copy the **Application Token** from Gotify into Uptime Kuma
7. Enter the **Server URL** like this: `http://localip:port`
8. To verify the notifications workss, click on **Test**

### Watchtower
To configure Watchtower notifications you will need to dive into your docker compose file.
There you will need to add the three lines.
```yaml
      - WATCHTOWER_NOTIFICATIONS=gotify
      - WATCHTOWER_NOTIFICATION_GOTIFY_URL=https://gotify.yourdomain.com
      - WATCHTOWER_NOTIFICATION_GOTIFY_TOKEN=GENERATED GOTIFY TOKEN
```
After setting Watchtower up you will get a notification when new images are available.  
And if you setup to automatically update and/or restart your container you will get a notification about it.  
Watchtower will every 24 hours for new iamges.  

### Speedtest-tracker
If you would like to get notifications about Speedtests which ran or about thereshold failures you can easily set this up in Speedtest Tracker.  
  
1. Login to Speedtest Tracker
2. Navigate to **Notifications**
3. Click on the to toggle under **Gotify** to enable Gotify notifications
4. Enable the desired triggers
5. Under **Webhooks** paste your Gotify URL as follows:  
`https://gotify.yourdomain.com/message?token=TOKEN`  

### Wallos
1. Login to your Wallos instance
2. Navigate to settings by hovering on your picture
3. Scroll to **Notifications**
4. Click on **Gotify**
5. Click on **Enable**
6. Insert your Gotify URL: `https://gotify.yourdomain.com`
7. Copy-paste your Gotify token
8. Lastely click on **Save**

### Beszel
1. Login to Beszel
2. Navigate to Settings -> Notications
3. Under Webhook/Push notification enter your Gotify URL below:  
`gotify:https://gotify.yourdomain.com/TOKEN`

## Resources
- [Gotify site](https://gotify.net/)
- [Gotify documentation](https://gotify.net/docs/index)
- [Gotify Github](https://github.com/gotify)