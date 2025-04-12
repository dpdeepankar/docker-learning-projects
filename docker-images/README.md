# Docker Images

A docker image is a lightweight, standalone, and read-only template used to create containers. it consists of - 
* An lightweight operating system (part of the OS kernel)
* Application code
* Dependencies of the application
* configuration files
* scripts or commands to run

## Docker Hub
Docker Hub is the public container registry hosted by Docker to store, manage and share docker images.
So when the image is not locally available, it is downloaded from <a href=https://hub.docker.com/>Docker Hub</a>

## Docker Hub Repository
A Docker Hub repository is a collection of container images, enabling you to store, manage, and share Docker images publicly or privately. Each repository serves as a dedicated space where you can store images associated with a particular application, microservice, or project. Content in repositories is organized by tags, which represent different versions of the same application, allowing users to pull the right version when needed.

Ref: <a href=https://docs.docker.com/docker-hub/repos/>Docker Hub repository docs</a>

## How do you access a image from docker hub.
By default, when you try running *docker run* command it look up the image in /var/lib/docker directory on the host machine. But if the docker image is not available locally then it is downloaded from the Docker Hub.

## What if we just want to pull the image.
To pull a image we can use *docker pull* command to download a image from docker hub. By default if we don't provide the repository name in the *docker pull* or *docker run* command, docker assumes you are pulling from the **default Docker Hub** and interpret it as

```
docker pull <repository>/image:tag
```

Now you would have noticed that at times you don't provide a repository or tag but still it works somehow. The rational behind this is that By default if we don't provide the repository name in the *docker pull* or *docker run* command, docker assumes you are pulling from the **default Docker Hub** and interpret it as

```
# Genrally you would used something like below for pulling a nginx image
docker pull nginx

# Docker interprets it as
docker pull docker.io/library/nginx:latest
```

## How do you list the images available locally?
We can use *docker image ls* command to get the list of images available/downloaded locally.

```
$ docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    7fc8925289a8   3 days ago     101MB
nginx         latest    1530d85bdba6   2 months ago   197MB
hello-world   latest    f1f77a0f96b7   2 months ago   5.2kB
```

## What is a docker image build of ?
Docker image is built using layers where each step taken to build the image. To see which steps are taken and what are the different layers in the image you can use *docker image history* command. This can be useful for troubleshooting issues with the container if the container is failed to start due to any configuration or command failure.

```
$ docker image history nginx
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
1530d85bdba6   2 months ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 months ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 months ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 months ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 months ago   COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
<missing>      2 months ago   COPY 20-envsubst-on-templates.sh /docker-ent…   3.02kB    buildkit.dockerfile.v0
<missing>      2 months ago   COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
<missing>      2 months ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   2.12kB    buildkit.dockerfile.v0
<missing>      2 months ago   COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
<missing>      2 months ago   RUN /bin/sh -c set -x     && groupadd --syst…   100MB     buildkit.dockerfile.v0
<missing>      2 months ago   ENV DYNPKG_RELEASE=1~bookworm                   0B        buildkit.dockerfile.v0
<missing>      2 months ago   ENV PKG_RELEASE=1~bookworm                      0B        buildkit.dockerfile.v0
<missing>      2 months ago   ENV NJS_RELEASE=1~bookworm                      0B        buildkit.dockerfile.v0
<missing>      2 months ago   ENV NJS_VERSION=0.8.9                           0B        buildkit.dockerfile.v0
<missing>      2 months ago   ENV NGINX_VERSION=1.27.4                        0B        buildkit.dockerfile.v0
<missing>      2 months ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 months ago   # debian.sh --arch 'arm64' out/ 'bookworm' '…   97.2MB    debuerreotype 0.15
```

In the above output you can see how the nginx image is built from different commands, and how they are layered on top of each step. All the configuration files and commands are packaged together into a small image of 197MB. Due to this small footprint, docker becomes really fast in spinning up a image.

There is another wonderful command that gives a detailed manifest of the docker image in JSON format.

```
docker image inspect nginx
```


## How would you share an image with others.
Suppose you have a running continaer that was initialized with a old image that is now unavailable. Or you might have built a image yourself and would want to share it with your peers. Then you can use *dokcer image save* command to store the image in tar format and then share with peers or store locally.

```
docker image save nginx -o nginx.tar
```


## How would you use this stored tar image
The tar file you have saved can then be loaded on any other machine using *docker image load* command.

```
$ docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    7fc8925289a8   3 days ago     101MB
hello-world   latest    f1f77a0f96b7   2 months ago   5.2kB


$ ls
docker-learning-projects  nginx.tar


$ docker image load -i nginx.tar
c9b18059ed42: Loading layer [==================================================>]  100.2MB/100.2MB
77e72e56d2dc: Loading layer [==================================================>]  101.2MB/101.2MB
3094e3f45803: Loading layer [==================================================>]  3.584kB/3.584kB
054c7ade68e5: Loading layer [==================================================>]  4.608kB/4.608kB
79e69c90ece3: Loading layer [==================================================>]   2.56kB/2.56kB
3e0d80cccd09: Loading layer [==================================================>]   5.12kB/5.12kB
455e93166cde: Loading layer [==================================================>]  7.168kB/7.168kB
Loaded image: nginx:latest


$ docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    7fc8925289a8   3 days ago     101MB
nginx         latest    1530d85bdba6   2 months ago   197MB
hello-world   latest    f1f77a0f96b7   2 months ago   5.2kB

```

## Deleting a docker image
With time, the number of images will grow and there would be many images that are not required anymore. During those times, you can delete the images using either *docker image rm* or *docker rmi* or *docker prune* command.

Commands like *docker image rm* and *docker rmi* command requires a image ID and can delte only one image at a time. 
NOTE: there shouldn't be any container lying around the image else deletion will fail.

```
$ docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    7fc8925289a8   3 days ago     101MB
nginx         latest    1530d85bdba6   2 months ago   197MB
hello-world   latest    f1f77a0f96b7   2 months ago   5.2kB

$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

$ docker image rm f1f77a0f96b7
Untagged: hello-world:latest
Untagged: hello-world@sha256:424f1f86cdf501deb591ace8d14d2f40272617b51b374915a87a2886b2025ece
Deleted: sha256:f1f77a0f96b7251d7ef5472705624e2d76db64855b5b121e1cbefe9dc52d0f86
Deleted: sha256:98a92b28c9b8d3cd4353801cba6ad181e86aaa616221e14739210acbda35b7fd

$ docker rmi 1530d85bdba6
Untagged: nginx:latest
Deleted: sha256:1530d85bdba64a05d90f5ed988c8f77ccf2cc8582027f3ad8a7c5b39a5c5e22b
Deleted: sha256:22c63c74e490d79eaeccee986cb27450856c1bf88ed12b4a010a21379f1c14e6
Deleted: sha256:13b9f0328e6a01e57bbb9b18fe45df4374a656b2d2bc17ea27611cb08c011f44
Deleted: sha256:60b49a45cdfb37a2ee770ef35af2dd73904f2165302abbcbf3ee11ed2c42bcbc
Deleted: sha256:4ec1e69669a09f5c95a46e0d24bc16d2b068fc6d9f56a99219dc10c2fc26e1ca
Deleted: sha256:e04526ccf613ba9fa8d5a86da4a219b8bc7e7342557dddfeda775fd3b26321a3
Deleted: sha256:08f9615550c8134bc617e7ead8294258253c305a721dad12d73427f9697f5172
Deleted: sha256:c9b18059ed426422229b2c624582e54e7e32862378c9556b90a99c116ae10a04

$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
ubuntu       latest    7fc8925289a8   3 days ago   101MB

```

If there are any containers either running or stopped, the image deletion will fail

```
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED        STATUS                    PORTS     NAMES
fef4125a4b7b   ubuntu    "/bin/bash"   11 hours ago   Exited (0) 11 hours ago             dockerRocks

$ docker image rm ubuntu
Error response from daemon: conflict: unable to remove repository reference "ubuntu" (must force) - container fef4125a4b7b is using its referenced image 7fc8925289a8
```

To delete multiple unused images AND untagged images at once you can use *docker image prune* command.
Note: This won't delete tagged images.
```
# we had all the tagged images hence the docker prun command results in no deletion

$ docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B



# But when combined with '-a' flag it forces the unused image deletion. 
$ docker image prune -a
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
untagged: nginx:latest
untagged: nginx@sha256:09369da6b10306312cd908661320086bf87fbae1b6b0c49a1f50ba531fef2eab
deleted: sha256:1530d85bdba64a05d90f5ed988c8f77ccf2cc8582027f3ad8a7c5b39a5c5e22b
deleted: sha256:22c63c74e490d79eaeccee986cb27450856c1bf88ed12b4a010a21379f1c14e6
deleted: sha256:13b9f0328e6a01e57bbb9b18fe45df4374a656b2d2bc17ea27611cb08c011f44
deleted: sha256:60b49a45cdfb37a2ee770ef35af2dd73904f2165302abbcbf3ee11ed2c42bcbc
deleted: sha256:4ec1e69669a09f5c95a46e0d24bc16d2b068fc6d9f56a99219dc10c2fc26e1ca
deleted: sha256:e04526ccf613ba9fa8d5a86da4a219b8bc7e7342557dddfeda775fd3b26321a3
deleted: sha256:08f9615550c8134bc617e7ead8294258253c305a721dad12d73427f9697f5172
untagged: redis:latest
untagged: redis@sha256:fbdbaea47b9ae4ecc2082ecdb4e1cea81e32176ffb1dcf643d422ad07427e5d9
deleted: sha256:9203d186d252274ab6e4d14e69eec83387717b4d8218c3bf9198771aa56ecd40
deleted: sha256:d164b156cf70e0743e2901b0a2790eeabbb9120b4bf1bcc98d9e2c7d1932a3d0
deleted: sha256:75eaab7c62040a5d7a706680eddd6568efeb9ae09bf8b9841632cfa8acce0394
deleted: sha256:6e3287fd5aa60f7812df752a5312f76f2ed40b21079ef47deca934b80e00e09a
deleted: sha256:91019256eb429e9d248adacaceefc6f41a31b0b58d020ebbe2002c0b8d2edcce
deleted: sha256:8daafcc07be98a8beab2ac08498df65d426ff66f0b077b0429f5aefda8be43b9
deleted: sha256:98cb803254610a622c60490832fc3ae3a9b70954e0f72a6929330c6e5d995e29
deleted: sha256:20f138e90c4ab119d4d5ca64c648e460ace23a33a89c1d5888ca70dcaf3d1542
untagged: httpd:latest
untagged: httpd@sha256:4564ca7604957765bd2598e14134a1c6812067f0daddd7dc5a484431dd03832b
deleted: sha256:fb5d0af0d31b10305517cc861ca7a7bfd7b0cb0ca1589faf9975c6c4323271d8
deleted: sha256:cebe12035c00efb5d11720e912082e5ee93394d8679bc2e3f06f20018303d8e0
deleted: sha256:469da78d9a7888ec18cef533873619557933c061a82182ddd57073b2c77f65d2
deleted: sha256:f35c0455adc7a27d9a878469ce78569ee5d318d49952f9725cccee0af3c5283c
deleted: sha256:ad4426191d1a3138f600f338c3e5f6713587cb90a9f0d5c66be15b3c7e5825ed
deleted: sha256:374e8c44df43b731c558d6486ebdfb6eaa87337ecdcfb8007f7befe194ce2d56
deleted: sha256:c9b18059ed426422229b2c624582e54e7e32862378c9556b90a99c116ae10a04

Total reclaimed space: 320.5MB

```


