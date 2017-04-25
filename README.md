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
```
docker stop $(docker ps -a -q)
```


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
docker volume create --driver netapp --name my_vol --opt size=10G
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

Now, let's create a volume for our Redis database. 
```
docker volume create --driver netapp --name vol_redis --opt size=50g
```
Make sure the volume has been created. 
```
docker volume ls
```
Now we can create our Redis server. 
```
docker run --name redis -d -v vol_redis:/data redis:3.2.6-alpine redis-server --appendonly yes
```
And check that our database container is running.
```
docker ps
```
To bring up our web server we need to link it to the Redis DB. 
```
docker run --name webapp -d -p 80:80 --link redis webapp_build:latest --redis_port=6379 --redis_host=redis
```

Now that we posted some logos on our webpage. Let's make a backup. 
```
docker stop redis
ssh admin@10.65.59.210 "volume snapshot create -vserver svm_jamaica_nas -volume netappdvp_vol_redis -snapshot netappdvp_vol_redis_snap1"
```
Show on in System Manager that the volume has now a snapshot. 

Upps something went wrong an we need to use the snapshot.
```
# remove the container pointing with the old volume
docker rm redis

# create a new volume from the snapshot we just created
docker volume create --driver netapp --name vol_redis_clone --opt from=vol_redis --opt fromSnapshot=netappdvp_vol_redis_snap1

# bring up the Redis DB with the new volume
docker run --name redis -d -v vol_redis:/data redis:3.2.6-alpine redis-server --appendonly yes
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
