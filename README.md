# docker-learning-projects
A collection  of handful docker projects targeting specific concepts of docker.

## Docker Engine Installation on Ubuntu
Docker engine is an open source containerization technology for building coontainerized applications.
A Docker engine consitutes of below primary components - 
* [dockerd] A daemon process that runs persistently on the system and enable us to manage containers.
* APIs through which we can interact with the docker daemon
* [docker] A command line interface (CLI) client 

## Installing docker engine
For learning purpose we will be using ubuntu as our base platform where we will install docker engine.
The steps are also available on docker engine docs, and we have followed same to setup docker engine.

### Setup_docker.sh
The bash script setup_docker.sh available in the git repo can be used to install docker engine on ubuntu.

```bash
setup_docker.sh
```

## Running your first container
Now that we have installed docker on our system we can use docker cli to run our fist app

```bash
docker run ubuntu
```

The above command will download the ubuntu image from the docker hub and run the container. 

command to see the running containers

```bash
docker ps
```

Surprisingly you won't find the ubuntu container in above command output. Reason is that the container runs to completion.
To view the executed container, use below command - 

```bash
docker ps -a
```

## Running a container in foreground and in detached mode
You must be wondering, though you ran the ubuntu container, why it exited out instead of keep on running.
When you run a Docker container using the docker run command you're instructing Docker to start and run the container .However, whether the container remains running or stops immediately after being started depends on the command that the container is configured to execute upon startup.
In this case, ubuntu container is not instructed to run anything. Let's pass a command to the container at the time of running it.

```bash
docker run ubuntu hostname
```

```bash
docker run ubuntu cat /etc/hosts
```

Let's take another session as well, on one session run the below command and on another run 'docker ps'. You will notice the ubuntu container will be seen as running.
```bash
docker run ubuntu sleep 15
```

In above cases, you would have seen that the command passed to the ubuntu container (hostname and cat /etc/hosts) return output from the container and then exits. Whereas in case of third command it exits after 15 seconds. Hence if there is such a command that keeps on running that will keep the contianer in running state.

Let's try with another image - nginx which is a web server.

```bash
docker run nginx
```

This command will start a nginx container in the foreground and you can see output from the nginx process. But you don't see a prompt which makes your terminal unusable. If you do a Ctrl+C here it will bring you back to your original session, but the nginx container is terminated. This is because the container was running in the foreground.

Let's run it in the background.
```bash
docker run nginx -d
```

the -d flag will run the container in detached mode meaning the container will run in the background. Now, if you run 'docker ps' it will show your nginx container as running.

You can even take a console of the nginx container and interact with the container. First lets get the container ID.
```bash
# Copy the container ID corresponding to the nginx container.
docker ps

#run the exec command to connect with the container interactively
docker exec -it <containerID> bash
```

docker exec command is used to execute a command on the contianer, in this case we are executing bash to open a bash terminal on the container. The -it flag instructs docke rdaemon to open the shell in interactive mode.

### View logs of a container
Now that we have started the container in the background/detached mode. If something goes wrong, how will we be able to troubleshoot it? where can we check logs?

```bash
docker logs <containerID>
```

The docker logs command is capable of extracting logs from a running as well as stopped containers.


## References

<a href=https://docs.docker.com/engine/>Docker Engine Docs</a>
