# fluentd-armv7-raspbpi

This project is referenced by a series of articles about [Developing and Deploying Kubernetes Applications on a Raspberry Pi Cluster](https://medium.com/better-programming/develop-and-deploy-kubernetes-applications-on-a-raspberry-pi-cluster-fbd4d97a904c). Specifically the article titled [Kubernetes Application Monitoring on a Raspberry Pi Cluster](https://medium.com/better-programming/kubernetes-application-monitoring-on-a-raspberry-pi-cluster-fa8f2762b00c). This repository contains a Docker file and associated supporting files to create a Docker image which contains a version of Fluentd that will run on an ARMv7 host. This image also includes the necessary Ruby Gem files to enable Fluentd to push logs to Elasticsearch. This repo was specifically developed (or hacked as it turns out) to be able to run Fluentd on a Raspberry Pi cluster. The files in this repo come from a variety of sources as follows:

1. `Dockerfile`, `entrypoint.sh`, and `fluent.conf` - These files were copied from [fluentd-docker-image](https://github.com/fluent/fluentd-docker-image) GitHub respository. Specifically from the `v1.9/armhf/debian` directory. All 3 were modified as will be described below.
2. `fluentd-daemonset.yaml` - This file was copied from [mjhea0/efk-kuberentes](https://github.com/mjhea0/efk-kubernetes) repo. 

# Modifications

1. `Dockerfile` - I added the `gem install` commands needed to install the Ruby Gems required to enable Elasticsearch support. I used the [fluent/fluentd-kubernetes-daemonset](https://github.com/fluent/fluentd-kubernetes-daemonset) as the reference for the required Ruby Gems. The [Gemfile](https://github.com/fluent/fluentd-kubernetes-daemonset/blob/master/docker-image/v1.9/debian-elasticsearch7/Gemfile)  contained the required information.
2. `entrypoint.sh` - This file is defined as the `ENTRYPOINT` for the Docker image. I added a block of code to define the `SIMPLE_SNIFFER` environment variable. This is also needed for Elasticsearch support.
3. `fluent.conf` - I added several `match` blocks per web resources referenced in the file.
4. `fluentd-daemonset.yaml` - I updated this file's container `image:` to reference the Docker image resulting from the previous 3 files. I also modified it to point to a specific Elasticsearch instance and resource limits.

There is a supporting Docker Hub repo containing a Docker image created from these files. It can be found at [roungkin/fluentd-kube-daemonset](https://hub.docker.com/repository/docker/ryoungkin/fluentd-kube-daemonset).