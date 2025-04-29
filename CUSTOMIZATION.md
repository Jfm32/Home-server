# Home-server

## Table of Contents
- [Installation](/README.md)
- [Customization](#Customization)
- [Sources](/SOURCES.md)



## Customization
### Add container to site

1. docker-compose.yml
  To add a container, go to [docker hub](https://hub.docker.com) and find what you want to add.
  On the page of the container you want to add you should find either a config like this
  ```
  services:
    foo:
      image: foo:latest
      container_name: foo
      restart: unless-stopped
      ports:
       - '8080:8080'
  ```
  Or you should have links to wikis where you will see this config.
  Add what you find with the **networks** to allow the nginx to redirect to this countainer.
  ```
  foo:
      image: foo:latest
      container_name: foo
      restart: unless-stopped
      ports:
       - '8080:8080'
      networks:
        - backend 
  ```
  At the end of the `docker-compose.yml` before
  ```
  networks:
    nginx-network:
      driver: bridge
    backend:
      driver: bridge
  ```

2. nginx/nginx.conf
  Add the following lines inside the balise `server` you want your page to be accessible from

  E.g: If you wan't it to be accessible on your-domain.name put it inside the server which hold `server_name your-domain.name`
  ```
  location /foo/ {
            auth_request /oauth2/auth;
            error_page 401 = @error401;   

            proxy_pass http://foo:8080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_set_header X-Auth-Request-Access-Token $upstream_http_authorization;
            proxy_set_header Authorization $http_authorization;
  }
  ```
> [!NOTE]  
> Don't forget to put the actual name of the container instead of **foo** and replace the port 8080 by the correct one. (If the port is already in use you can simply use another one but don't forget to put it inside the docke-compose.yml and nginx.conf.

3. Add a button to the page <br/>
   Go to `Home-server/hub/templates/index.html` and add the following
   ```
   <a href="/foo/" class="button">foo</a>
   ```
Restart the server and you are good to go.

### Remove container to the site
Following the previous setup we just saw remove the block referencing the container you want to remove inside the nginx.conf, docker-compose.yml and the index.html.
Then when you restart the docker use 
```
sudo docker compose up --remove-orphans
```

### Add container to the server
If you want to add something like pihole, game server, etc or a docker container that doesn't need a web access:
  - follow the setp 1 of the installation on the server
  - allow the connection on the firewall if you have one
  - allow the port forwarding to the port used by the container like seen in [Installation](/README.md)

