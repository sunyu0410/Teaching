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
