# Docker Demo
Introduction to Docker  and the NetApp Docker Volume Plugin (nDVP)

## Instructions
###Step 1: Hello World
Show the hello-world container
```
docker run hello-world
```

Show running containers:
```
docker ps
```

Show images:
```
docker images ls
```


###Step 2: A simple service in a containers
GoTo Folder demo01Web

Show and explain ```Dockerfile```
Show code folder that is copied into the container
Build the container
```
docker build -t halloweltwebserver:v1 .
```
and run it
```
docker run -d -p 80:80 halloweltwebserver:v1
```

Open Browser and show Website on Host-IP:80

Show how more instances can be startet. No download is needed since images are already here.

```
docker run -d -p 5000:80 halloweltwebserver:v1
```
Open this Webserver at Host-IP:5000

Stop everything!!!  

###Step3: Basics in the NetApp-Docker-Volume Plugin
Tip: Install before the demo. In case of problems.

But show how easy it is to install the plugin by open the website and highlighting the command.
https://store.docker.com/plugins/netapp-docker-volume-plugin-ndvp?tab=description
Then go to the host and show the config-file that is used when installing the plugin.

```
cat /etc/netappdvp/config.json
```

Show persistent volumes:
```
docker volume ls
```

Now manually create a new volumes
```
docker volume create -d netapp --name my_vol --opt size=10G
docker volume ls
```

Show the created volume via SystemManager on the connected NetApp System


###Step4: A complete application stack with different services per container

Goto the demo02App - Folder

Show ```Dockerfile```

First, build the current codebase:
```
docker build . -t "webapp_build"
```

Let's make sure it was built successfully:
```
docker images
```

Show and explain the ```docker-compose.yml```
This is the Application Stack. A webserver, the code and Database resing on a netapp volume.

Let's show our current Docker Volumes:
```
docker volume ls
```

Bring up the application stack:
```
docker-compose up
```

Show the new created netapp volume.
Show the application.
