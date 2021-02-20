# ubuntu:18.04 설치
FROM ubuntu:18.04

# Updating Ubuntu packages
RUN apt-get update && yes|apt-get upgrade -y
RUN apt-get update && yes|apt-get install -y vim
RUN apt-get install -y emacs
# Adding wget, bzip2, nodejs and git
RUN apt-get install -y wget bzip2 git
RUN apt-get update
RUN apt-get install -y --no-install-recommends python3.6 python3.6-dev python3-pip python3-setuptools python3-wheel gcc

RUN python3.6 -m pip install pip --upgrade

RUN pip install numpy
RUN pip install scikit-learn
RUN pip install pyspark
RUN pip install jupyter notebook
RUN pip install opencv-python

WORKDIR /home/brunch