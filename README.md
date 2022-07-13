# ResNet-ROS2 Wrapper

This is a ROS2 wrapper for the Residual Network, [ResNet](https://arxiv.org/abs/1512.03385). We utilize `huggingface` and the `transformers` for the [source of the algorithm](https://huggingface.co/microsoft/resnet-50). The main idea is for this container to act as a standalone interface and node, removing the necessity to integrate separate packages and solve numerous dependency issues. We have built all different versions of the algorithm (i.e. `resnet-50`, `resnet-101`, etc.) for each ROS2 distribution.

Model is trained on the ImageNet-1000 dataset.

# Installation Guide

## Using Docker Pull
1. Install [Docker](https://www.docker.com/) and ensure the Docker daemon is running in the background.
2. Run ```docker pull shaderobotics/resnet:${ROS2_DISTRO}-${MODEL_VERSION}``` we support all ROS2 distributions along with all model versions found in the model version section below.
3. Follow the run commands in the usage section below

## Build Docker Image Natively
1. Install [Docker](https://www.docker.com/) and ensure the Docker daemon is running in the background.
2. Clone this repo with ```git pull https://github.com/open-shade/resnet.git```
3. Enter the repo with ```cd deit```
4. To pick a specific model version, edit the `ALGO_VERSION` constant in `/resnet/resnet.py`
5. Build the container with ```docker build . -t [name]```. This will take a while. We have also provided associated `cloudbuild.sh` scripts to build on GCP all of the associated versions.
6. Follow the run commands in the usage section below.

# Model Versions

* ```resnet-50```
* ```resnet-101```
* ```resnet-18```
* ```resnet-34```
* ```resnet-152```

More information about these versions can be found in the [paper](https://arxiv.org/abs/1512.03385). `50`, `101` represent the size of the model.

## Example Docker Command

```bash
docker pull shaderobotics/resnet:foxy-resnet-50
```

# Usage
## Run the ResNet Node 
Run ```docker run -t --net=host shaderobotics/resnet:${ROS_DISTRO}-${MODEL_VERSION}```. Your node should be running now. Then, by running ```ros2 topic list,``` you should see all the possible pub and sub routes.

For more details explaining how to run Docker images, visit the official Docker documentation [here](https://docs.docker.com/engine/reference/run/). Also, additional information as to how ROS2 communicates between external environment or multiple docker containers, visit the official ROS2 (foxy) docs [here](https://docs.ros.org/en/foxy/How-To-Guides/Run-2-nodes-in-single-or-separate-docker-containers.html#). 

# Topics

| Name                   | IO  | Type                             | Use                                                               |
|------------------------|-----|----------------------------------|-------------------------------------------------------------------|
| resnet/image_raw       | sub | [sensor_msgs.msg.Image](http://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/Image.html)            | Takes the raw camera output to be processed                       |
 | resnet/result           | pub | String            | Outputs the classification label from ImageNet 1000 Classes as a string |

# Testing / Demo
To test and ensure that this package is properly installed, replace the Dockerfile in the root of this repo with what exists in the demo folder. Installed in the demo image contains a [camera stream emulator](https://github.com/klintan/ros2_video_streamer) by [klintan](https://github.com/klintan) which directly pubs images to the ResNet node and processes it for you to observe the outputs.

To run this, run ```docker build . -t [name]```, then ```docker run --net=host -t [name]```. Observing the logs for this will show you what is occuring within the container. If you wish to enter the running container and preform other activities, run ```docker ps```, find the id of the running container, then run ```docker exec -it [containerId] /bin/bash```