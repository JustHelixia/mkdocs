# Docker information

### Updating Docker Compose images:
1. `sudo docker compose down`
2. `sudo docker compose pull`
3. `sudo docker compose up -d`
If you forget `-d` then you cannot run Docker detached which causes you not being able to logout. Just don´t forget `-d`.
  
### Updating Docker image with the Docker CLI
1. `docker pull IMAGENAME:TAG`
2. `docker stop CONTAINER-NAME`
3. `docker rm CONTAINER-NAME`
4. `docker run -d --restart=always -p PORT:PORT -v path/to/app/data -name CONTAINER-NAME IMAGE`
  
When inspect the Docker run command you notice similarities with a Docker Compose file:  
- -d -> detached mode  
- --restart=always -> is the restart policy  
- -p -> stands for the ports needs to be used external and internal  
- -v -> this stands for volume to set Docker volumes  
- -name -> is the name of the servive  
- IMAGE -> this is the last part where you define which image you are going to use  