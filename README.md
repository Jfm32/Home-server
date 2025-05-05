# Home-server

A simple to setup home-server easly customizable. The all purpose of the project is to make it has easy has possible to setup a home server on an old computer using OVH domain name and Github to connect to the services. The main guide on how to setup the server and use the different apps already build in his aimed at low experience to new user of docker and the apps used. 

In a way it's the self made Arr suite with extra information on how to push-it in production with good security.

> [!WARNING]  
> This setup can cause a lowered quality of wifi connection on your network. 
 
## Table of Contents
- [Installation](#installation)
- [Customization](/CUSTOMIZATION.md)
- [Usage](#usage)
- [Sources](/SOURCES.md)
- [License](#license)


## Prerequisites
- An old computer running Windows, macOS, or Linux operating system
- Docker installed on the server: [official site](https://docs.docker.com/compose/install/) 
- A domain name bought from [OVH](https://www.ovhcloud.com/)
- Forward port 80(http) and 443(https) from router to the server.
> [!TIP]
> You must have a static ip for the server those port forward to this ip for the server to work. Usually you can make this configuration on your router by going to http://192.168.0.1 when on the wifi.


## Recommandations
You should add an acces to your sever on the port 22(ssh) to be able to connect to it for troubleshooting. 

> [!WARNING]  
>  If you do open ssh forwarding on your server, you HAVE to disable password connection and allow only private key connection.


## Installation 
1. Download the repository
   ```
   git clone https://github.com/Jfm32/Home_server.git
   cd Home_server
   ```

## Usage
To run the server, you have to put the server information into the following place: 
1. **./.env** file
   ```
   # .env file

   #domain and email for SSL certficiates
   DOMAIN=your-domain.name
   EMAIL=your@e.mail

   #OAUTH2
   CLIENT_ID=xxxxx
   CLIENT_SECRET=xxxxx
   COOKIE_SECRET=xxxxx
   ```
   You have to replace `your-domain.name` and `your@e.mail` with your actual ones. 
   For `COOKIE-SECRET` you have to paste the result of the following command 
   ```
   openssl rand -base64 32
   ```
   For `CLIENT_ID` and `CLIENT_SECRET`, you have to go to :
   - [GitHub developpers page](https://github.com/settings/developers)
   - Create New Oauth App
   - Inside `Homepage URL` put https://your-domain.name
   - Inside `Authorizaton callback URL` you have to put https://your-domain.name/oauth2/callback
   - Then copy and paste the **CLIENT_ID** and generate a **CLIENT_SECRET** and paste it also
     ![Exemple](/.github/Images/oauth2-github.jpeg)

2. **./certbot/.ovhapi**
   ```
   #./certbot/.ovhapi file
   dns_ovh_endpoint = ovh-eu
   dns_ovh_application_key = xxxxx
   dns_ovh_application_secret = xxxxx
   dns_ovh_consumer_key = xxxxx
   ```
   You have can go to [OVH api setup article](https://help.ovhcloud.com/csm/en-api-getting-started-ovhcloud-api?id=kb_article_view&sysparm_article=KB0042777)
   Follow the isntructions there under the section `Advanced usage: pair OVHcloud APIs with an application -> Create your app keys`
   Then just copy and paste the key to the rigth varibale inside the .ovhapi file

3. **./nginx/nginx.conf**
   Inside this file you will find:
   ```
   server {
        listen 443 ssl;
        http2 on;
        server_name your-domain.name;

        ssl_certificate     /etc/letsencrypt/live/your-domain.name/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/your-domain.name/privkey.pem;
   ```
   You would have to replace all the mention to **your-domain.name** to your actual domain name.
   
> [!TIP]
> You should have 6 references of this instance, but you can also add next to `your-domain.name` `www.your-domain.name` to access the site on both url

5. **Launch**
   To launch the app you can just run
   ```
   sudo docker compose up -d
   ```
   
> [!TIP]
> When lauching with -d you have to use sudo docker logs qbittorrent to see the password for the first login 
  To stop the containers you have
  ```
  sudo docker compose down
  ```
Now if you want to go further you can see the [Customization](/CUSTOMIZATION.md).
