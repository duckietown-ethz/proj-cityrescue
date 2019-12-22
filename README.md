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


### Map
After building a Duckietown, the map should be recorded following [this procedure](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_map_making.html)

### Autobots
This part is mentioned below in [Duckiebot setup notes](#duckiebot-setup-notes).

### The localization system
#### Ground AprilTags
Ground AprilTags are essential and should be added to the Autolab following the instructions [here](https://docs.duckietown.org/daffy/opmanual_autolab/out/localization_apriltags_specs.html). As also mentioned in the link, the tags should be added to the [map created](#map-creation)

#### Watchtowers
* Build, initialize and calibrate the watchtowers following instructions [here](https://docs.duckietown.org/daffy/opmanual_autolab/out/watchtower_hardware.html)
* Place watchtowers in the Autolab with [this guideline](https://docs.duckietown.org/daffy/opmanual_autolab/out/localization_watchtower_placement.html)
* Prepare the docker images on watchtowers. For each watchtower, run following commands on lab server:
    * :computer: `docker -H <HOSTNAME>.local rm -f dt18_03_roscore_duckiebot-interface_1`
    * :computer: `docker -H <HOSTNAME>.local pull duckietown/dt-duckiebot-interface:daffy-arm32v7`
    * :computer: `docker -H <HOSTNAME>.local pull duckietown/acquisition-bridge:daffy-arm32v7`

#### Lab server
* Pull existing docker images needed
    * :computer: `docker pull duckietown/dt-ros-commons:daffy-amd64`
    * :computer: `docker pull duckietown/dt-autolab-rviz`
    * :computer: `docker pull duckietown/apriltag-processor:daffy-amd64`
    * :computer: `docker pull duckietown/wheel-odometry-processor:daffy-amd64`
    * :computer: `docker pull duckietown/cslam-graphoptimizer:daffy-amd64`
* In addition to the localization software, one should also clone the developed repositories onto the lab server
    * :computer: `git clone` all 4 submodule repositories
*  And among the developed repositories, build 2 of the server side images
    * In terminal, navigate to the ___duckie-rescue-center___ repository. Then run :computer: `dts devel build -f --arch amd64`
    * In terminal, navigate to the ___simple-localization___ repository. Then run :computer: `dts devel build -f --arch amd64`

## Duckiebot setup notes
### Hardware
The Duckiebot should be modified according to the [Autobot specification](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_autobot_specs.html), without the autocharging part.

### Software
Among the developed repositories cloned to the lab server, build 2 of the Duckiebot side images:
* In lab server terminal, navigate to the ___dt-core-cityrescue___ repository. Then run :computer: `dts devel build -f --arch arm32v7 -H <HOSTNAME>.local`
* In lab server terminal, navigate to the ___rescue-acquisition-bridge___ repository. Then run :computer: `dts devel build -f --arch arm32v7 -H <HOSTNAME>.local`

## Pre-flight checklist
- [x] The Autolab map yaml file is created, with ground AprilTags.
- [x] The watchtowers are placed to properly cover the city.
- [x] The watchtowers have the required _dt-duckiebot-interface_ and _acquisition-bridge_ images.
- [x] The lab server has the required images, either pulled or built from the developed repositories.
- [x] The Autobots have the required _dt-core-cityrescue_ and _rescue-acquisition-bridge_ built from the developed repositories.

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
