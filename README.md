# proj-cityrescue
AMoD - City Rescue

This readme file serves as an operation manual for running the entire city rescue demo. The documentation (explanation + how-to's) for each of the four submodules is within their own repository readme.

# Visualization
  docker run -it --rm --net=host --env="DISPLAY" -e ROS_MASTER=duckietown9 -e ROS_MASTER_IP=192.168.1.187 -e DUCKIETOWN_WORLD_FORK=jasonhu5 -e MAP_NAME=ethz_amod_lab_k31 duckietown/dt-autolab-rviz


# Utils
dts duckiebot keyboard_control autobot27
dts start_gui_tools duckietown9

## acquistion bridge
docker -H autobot27.local run --name rescue-acquisition-bridge --rm --network=host -v /data:/data -e LAB_ROS_MASTER_IP=192.168.1.36 -dit duckietown/rescue-acquisition-bridge:daffy-arm32v7

## Parameter
docker run -it --rm --net host duckietown/dt-ros-commons:daffy-amd64 /bin/bash
dts start_gui_tools duckietown9

* To add bot to be monitored by rescue system
rosparam set /rescue/rescue_center/add_duckiebot 27
* To remove bot from being monitored by rescue system
rosparam set /rescue/rescue_center/remove_duckiebot 27
