# proj-cityrescue
AMoD - City Rescue

This readme file serves as an operation manual for running the entire city rescue demo. The documentation (explanation + how-to's) for each of the four submodules is within their own repository readme.

## Video of expected results
[Link to the demo video](https://drive.google.com/open?id=15cX4IMYtRQwWZJWpyGWcul3IyHbU7Ozu)

In the demo video, the Duckiebot with following 4 distress situations was rescued:
* out of straight lane
* stuck (with infrastructure)
* wrong heading (possibly not on correct side of lane)
* out of curve lane

## Duckietown setup notes
The Duckietown should be setup as an [Autolab](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_definition.html), with following requirement:

* A map
* A fleet of Autobots 
* A localization system
    * Ground AprilTags
    * Watchtowers
    * The localization software on lab server(s)


### Map creation
After building a Duckietown, the map should be recorded following [this procedure](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_map_making.html)

### Autobots
This part is mentioned below in [Duckiebot setup notes](#duckiebot-setup-notes).

### The localization system
#### Ground AprilTags
Ground AprilTags are essential and should be added to the Autolab following the instructions [here](https://docs.duckietown.org/daffy/opmanual_autolab/out/localization_apriltags_specs.html). As also mentioned in the link, the tags should be added to the [map created](#map-creation)

#### Watchtowers



## Duckiebot setup notes

## Pre-flight checklist

## Demo instructions

## Troubleshooting

## Potential demo failure scenarios

[Offset Measurement](https://github.com/jasonhu5/simple-localization/tree/ba1ca03a1832c76466e99deb14e21ca13adf14d2#also-required-offset-measurement-for-watchtowers)






<!-- 
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
rosparam set /rescue/rescue_center/remove_duckiebot 27 -->
