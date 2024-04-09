# Introduction
* Docker is the best way to make portable software
* It's not virtual machines
    * A virtual machine simulates the entire OS
    * Docker simulates the processes on your original OS

## How it works
* Define a recepie: this is call `Dockerfile`, which is a text file telling what commands you need to run. It's a more powerful version of shell script.
    * You can start with a base image, then build on it.
* Build an binary using `Dockerfile`: this is call an **image**. 
* Execute an image in a runtime session: this is called a **container**. A container will be stopped once you exit from it.

## Example
Say on Ronin Ubuntu 20.04 we want to get a Python 3.10 on a Ubuntu 18.04 with some packages.

1. Install Docker
```shell
# Ubuntu 20.04 LTS
sudo passwd # set a password
su          # Switch to root user

apt update
apt upgrade
apt install docker.io

systemctl status docker # Check it's running
docker run hello-world  # Verify it can build and run images
```


If you see something like this, then it's correct.

```
root:~# docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
...
```

2. Define a `Dockerfile`. Put the following text in a text file called `Dockerfile`.
```
FROM ubuntu:18.04

RUN apt-get update
RUN apt-get -y upgrade
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get install -y wget build-essential libreadline-gplv2-dev libncursesw5-dev \
     libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev

RUN mkdir /python_src
WORKDIR /python_src
RUN wget https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz
RUN tar xzf Python-3.10.0.tgz
RUN Python-3.10.0/configure --enable-optimizations
RUN make altinstall

RUN ln -s /python_src/python /usr/bin/python
RUN wget https://bootstrap.pypa.io/get-pip.py

RUN python get-pip.py

RUN pip install SimpleITK pydicom
```

3. Build the image

`docker build .`

It will take about 5 minutes. Once done, you can see the images:
```
root@ip-10-0-4-57:~# docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
<none>        <none>    3fcc4095982d   3 minutes ago   1.42GB
ubuntu        18.04     f9a80a55f492   10 months ago   63.2MB
hello-world   latest    d2c94e258dcb   11 months ago   13.3kB
```

The first row is the one just built. Notice each image has a unique ID. Yours can be different.

4. Run the image

`docker run -it 3fcc4095982d`

It will enter a Linux command line, which is Ubuntu 18.04. Python is there with the installed packages.

```
root@ip-10-0-4-57:~# docker run -it 3fcc4095982d
root@8bc42910cd18:/python_src# python
Python 3.10.0 (default, Apr  9 2024, 01:42:07) [GCC 7.5.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import SimpleITK as sitk
>>> from pydicom import read_file
>>>
```
