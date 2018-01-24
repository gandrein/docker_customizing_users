## Docker Image Customization Example

This repository is an example of a Docker Image customization having as base image either
* Ubuntu 16.04 (Xenial)
* Ubuntu 16.04 (Xenial) with Nvidia CUDA toolkit and cuDNN library

The image is customized such that:
* the docker image is created with a non-root user (defaul user-name `docker`)
* [Oh My ZSH](http://ohmyz.sh/) is installed and configured for the non-root user
* docker containers are launched with [`terminator`](https://gnometerminator.blogspot.nl/p/introduction.html) as the default terminal emulator (as opposed to default `gnome-terminal`)
* host mounted volumes are re-bound to a `data` folder within the container's user's `$HOME` folder 
* bash completion for Docker image names and tags when launching the container by using the `./run.sh` script

The user has the option of building three different types of Docker images
1. a generic image for non-Nvidia based PCs
2. an image with support for Nvidia Graphics and OpenGL by using [nvidia-docker (1.0)](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-1.0))
3. an image with the Nvidia CUDA toolkit and cuDNN library

### Purpose

Experimentation ...

### Requirements
This docker image has been build and tested on a machine running Ubuntu 16.04 with `docker version 17.09.0-ce`

####  Dependencies

For the 2. and 3. type of images to succesfully run GUIs or make use of the cuDNN library, the user must install  [`nvidia-docker (1.0)`](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-1.0))

### Building the image

#### Makefile based

The [Makefile](Makefile) contained in the repository allows you to create either of the three images with the `make` command. The `make` utility will provide the autocompletion for the image name you want to build. 

#### Manual / Custom Names

If a custom name is preferred rather than the default one assigned by the `Makefile`, use the `./build.sh` script contained in each subfolder, e.g. [generic/.build.sh](./generic/build.sh). This script will build a docker image with the name provided as the first argument and with a specified `non-root` user (`default=docker`) for the docker container.

```
./build.sh GIVEN_IMAGE_NAME
```

#### The ENTRYPOINT `entrypoint.sh` script
For each image / subfolder, the bash script [entrypoint.sh](./entrypoint.sh) will be copied at build time into the Docker image and will be ran as the default _entrypoint_ when the container is launched. The script currently _bind mounts_ the Dockerfile's `extern` _volume_ into the `$HOME/host_files` folder of the `non-root` user.

### Running a container

Navigate to the chosen image subfolder. In a terminal type [./run.sh](./run.sh) followed by the name chosen in the previous step or with the default name assigned by the `Makefile`. This will run and remove the docker container upon exit (i.e., it is ran with the `--rm` flag).
```
./run.sh GIVEN_IMAGE_NAME
```
e.g. for the second Docker image created with the `make` command, do
```
./run.sh codookie/xenial:terminator-nvidia 
```

Whatever data you require from the host, mount it in the container's `extern` folder, e.g. following the bellow structure 
```
  -v $HOME/Projects/ROS:/extern/ROS 
```
When the container is launched, the data in the `extern` folder will also be available in the docker user's `$HOME/host_files` folder. 

The image also shares the X11 unix socket with the host and its network interface.

#### Running Firefox from within the container

Run the container and install Firefox from the terminal, 
```
sudo apt install firefox
```
After installation, launch Firefox from the terminal. You are now running Firefox from a Docker container :smiley:.

### Bash auto-completion for `./run.sh`

When using `./run.sh` in a bash shell to launch the container, source the [bash_custom_completion.sh](./configs/bash_docker_images_completion.sh) script. Now you should be able to get the names of the available docker images on your system whenever you type 
```
./run.sh <TAB><TAB>
```


## References

The `Oh My ZSH`, `terminator` configuration and `Makefile` structure based on 
* [turlucode/ros-docker-gui/](https://github.com/turlucode/ros-docker-gui/)

The script(s) structure and user creation adapted from
* [jbohren/rosdocked](https://github.com/jbohren/rosdocked)
