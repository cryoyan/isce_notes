# Instructions for running and installing ISCE with Docker

## Last tested July 2018 with ISCE 2.2.0 and Docker 18.03.1-ce


These instructions are for installing ISCE on Ubuntu 18.04. 
Docker allows you then run ISCE on any machine (MacOSX, CentOS server, AWS EC2, etc).
The resulting image takes up about 700 Mb of disk space.

## Create a Docker Image
```
wget https://imaging.unavco.org/software/ISCE/isce-2.2.0.tar.bz2
docker build --rm -t isce:v2.2.0 . 
or 
docker build -t isce2 . 
```

## Run ISCE in an interactive Docker Container
```
docker run -it --rm isce:v2.2.0 /bin/bash
```
## Run ISCE on the host machine (ubuntu)
```
docker run -d -v $HOME/Data:$HOME/Data --user 1002:1002 -w=$HOME/Data -it isce2
# -d: detach after start the docker
# --user: assign the user and group id (get by 'cat /etc/passwd | grep $(whoami)') on the host machine
# $HOME/Data: the workdir where you are going to play with ISCE

# get container id (-q: only get the id, -l: get the latest one)
container_id=$(docker ps --filter "ancestor=isce2:latest" -q -l)
# run ISCE outside the docker container
docker exec -it ${container_id} insarApp.py
```

## Mapping a local folder (where you have SAR data stored)
```
docker run -it --rm -v /local/path:/tmp isce:v2.2.0 /bin/bash
```

## Using MDX (this works on MacOSX
```
ip=$(ifconfig en0 | awk '/inet /{print $2 ":0"}')
xhost + 
docker run -it --rm -e DISPLAY=$ip -v /my/local/data:/tmp/data -v /tmp/.X11-unix:/tmp/.X11-unix isce:v2.2.0 /bin/bash
```
