# proj-cityrescue
AMoD - City Rescue

This readme file serves as an operation manual for running the entire city rescue demo. The documentation (explanation + how-to's) for each of the four submodules is within their own repository readme.

## Video of expected results
[Link to the demo video](https://drive.google.com/open?id=1ohhhH0gU1xY_ijCcwv1GKPr7JTJjut_k), the full demo starts at 01:42

In the demo video, the Duckiebot with following 4 distress situations was rescued:
* out of straight lane
* stuck/crashed (with infrastructure)
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
* In terms of network connection, the watchtower should be connected to the lab server machine through cabled Ethernet LAN.

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
    * In terminal, navigate to the ___simple-localization___ repository. In `packages/localization/src` directory, modify the file `bot_tag_mapping` by adding the (Autobot tag ID):(Autobot number) in the file. This files is responsible for mapping a recognized AprilTag to an Autobot, so make sure the demo Autobots are added. Then run :computer: `dts devel build -f --arch amd64`

## Duckiebot setup notes
### Hardware
The Duckiebot should be modified according to the [Autobot specification](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_autobot_specs.html), without the autocharging part.

### Software
Among the developed repositories cloned to the lab server, build 2 of the Duckiebot side images:
* In lab server terminal, navigate to the ___dt-core-cityrescue___ repository. Then run :computer: `dts devel build -f --arch arm32v7 -H <HOSTNAME>.local`
* In lab server terminal, navigate to the ___rescue-acquisition-bridge___ repository. Then run :computer: `dts devel build -f --arch arm32v7 -H <HOSTNAME>.local`

## Pre-flight checklist
- [x] The Autolab map yaml file is created on your GitHub fork of duckietown-world, with ground AprilTags.
- [x] The watchtowers are placed to properly cover the city.
- [x] The watchtowers have the required _dt-duckiebot-interface_ and _acquisition-bridge_ images.
- [x] The lab server has the required images, either pulled or built from the developed repositories.
- [x] The Autobots have the required _dt-core-cityrescue_ and _rescue-acquisition-bridge_ built from the developed repositories.

## Demo instructions
All commands are run from the lab server terminal, and the :computer: mark will be omitted. For the placeholders in the following commands, a list of clarifications/examples is provided after the command.

### Start the online localiztion system

#### Start a ros master
`docker run --name roscore --rm --net=host -dit duckietown/dt-ros-commons:daffy-amd64 roscore`

If this container stopped, all ros nodes that communicate on this master node should be restarted.

#### Setup visualization
First run: `xhost +`

Then run: 
```
docker run -it --rm --net=host --env="DISPLAY" \
-e ROS_MASTER=<LAB_SERVER_HOSTNAME> \
-e ROS_MASTER_IP=<LAB_SERVER_IP> \
-e DUCKIETOWN_WORLD_FORK=<YOUR_FORK> \
-e MAP_NAME=<YOUR_MAP> \
duckietown/dt-autolab-rviz
```
* _LAB_SERVER_HOSTNAME_: e.g. `duckietown9`
* _LAB_SERVER_IP_: e.g. `192.168.1.187`
* _YOUR_FORK_: your GitHub duckietown-world fork (where the Autolab map is created). For ETHZ AMoD Lab, the fork is `jasonhu5`
* _YOUR_MAP_: the map file name, without ".yaml" suffix. For ETHZ AMoD Lab, the map name is `ethz_amod_lab_k31`

___Expected outcome___: Now a rviz window will pop up. It loads and displays the map one specified in above command. On left side bar, the path names should be modified to match the actual autobot names.

#### Prepare watchtowers
First start the required version of _dt-duckiebot-interface_. For each watchtower, run:
```
docker -H <HOSTNAME>.local run --name duckiebot-interface \ --privileged -e ROBOT_TYPE=watchtower \
--restart unless-stopped -v /data:/data \
-dit --network=host \
duckietown/dt-duckiebot-interface:daffy-arm32v7
```
* _HOSTNAME_: e.g. `watchtower101`

Then start the acquisition bridge. For each watchtower, run:
```
docker -H <HOSTNAME>.local run --name acquisition-bridge \
--network=host -e ROBOT_TYPE=watchtower \
-e LAB_ROS_MASTER_IP=<LAB_SERVER_IP> \
-dit duckietown/acquisition-bridge:daffy-arm32v7
```
* _HOSTNAME_: e.g. `watchtower101`
* _LAB_SERVER_IP_: e.g. `192.168.1.187`

#### Run rescue acquisition bridges on Autobots
This is our version acquisition bridge. For each Autobot, run:
```
docker -H <HOSTNAME>.local run \
--name rescue-acquisition-bridge \
--rm --network=host -v /data:/data \
-e LAB_ROS_MASTER_IP=<LAB_SERVER_IP> \
-dit duckietown/rescue-acquisition-bridge:daffy-arm32v7
```
* _HOSTNAME_: e.g. `autobot27`
* _LAB_SERVER_IP_: e.g. `192.168.1.187`

#### Start apriltag-processors
It is suggested [here](https://docs.duckietown.org/daffy/opmanual_autolab/out/localization_demo.html) to split up the apriltag processors on multiple lab machines. For each watchtower, run 
```
docker run --name apriltag_processor_<WATCHTOWER_NUMBER> \
--network=host -dit --rm  \
-e ROS_MASTER_URI=http://<LAB_SERVER_IP>:11311 -e ACQ_DEVICE_NAME=<WATCHTOWER_NAME> \
duckietown/apriltag-processor:daffy-amd64
```
* _WATCHTOWER_NUMBER_: for watchtower101, it's `101`
* _LAB_SERVER_IP_: the IP of the lab server that is running ros master, e.g. `192.168.1.187`
* _WATCHTOWER_NAME_: watchtower hostname, e.g. `watchtower101`

#### Start wheel-odometry-processors

For each Autobot, run
```
docker run --name odometry_processor_<AUTOBOT_NUMBER> \
--network=host -dit --rm  \
-e ACQ_ROS_MASTER_URI_SERVER_IP=<LAB_SERVER_IP> \
-e ACQ_DEVICE_NAME=<AUTOBOT_NAME> \
duckietown/wheel-odometry-processor:daffy-amd64
```
* _AUTOBOT_NUMBER_: for autobot27, it's `27`
* _LAB_SERVER_IP_: the IP of the lab server that is running ros master, e.g. `192.168.1.187`
* _AUTOBOT_NAME_: bot hostname, e.g. `autobot27`

#### Finally, start the online localization
```
docker run --rm \
-e OUTPUT_DIR=/data \
-e ROS_MASTER=<LAB_SERVER_HOSTNAME> \
-e ROS_MASTER_IP=<LAB_SERVER_IP> \
--net=host --name graph_optimizer \
-v <PATH_TO_RESULT_FOLDER>:/data \
-e DUCKIETOWN_WORLD_FORK=<YOUR_FORK> \
-e MAP_NAME=<YOUR_MAP> \
duckietown/cslam-graphoptimizer:daffy-amd64
```
* _LAB_SERVER_HOSTNAME_: e.g. `duckietown9`
* _LAB_SERVER_IP_: e.g. `192.168.1.187`
* _PATH_TO_RESULT_FOLDER_: the local path where result files should be created (this does not really matter for city rescue) e.g. ~/ProjectCityRescue
* _YOUR_FORK_: your GitHub duckietown-world fork (where the Autolab map is created). For ETHZ AMoD Lab, the fork is `jasonhu5`
* _YOUR_MAP_: the map file name, without ".yaml" suffix. For ETHZ AMoD Lab, the map name is `ethz_amod_lab_k31`

In the rviz window, add by topic the `/cslam_markers` topic.

___Expected outcome___: after above steps, if one drives the Autobot through the Autolab, in the rviz visualization window, the watchtowers it went past, and a path of the bot will be displayed.

### Run the simple localization system

Start the container: `docker run --name localization --network=host -it --rm duckietown/simple-localization:v1-amd64`

As described [here](https://github.com/jasonhu5/simple-localization/tree/ba1ca03a1832c76466e99deb14e21ca13adf14d2#system-explanation), an offset measurement step is needed. Please follow the steps described [here](https://github.com/jasonhu5/simple-localization/tree/ba1ca03a1832c76466e99deb14e21ca13adf14d2#also-required-offset-measurement-for-watchtowers) to compensate the offset for the watchtowers that one intend to use.

After the offset measurement procedure is performed for some watchtowers, in the rviz visualization window, add by topic the `/simple_loc` markers.

___Expected outcome___: when one moves the autobot near a offset compensated watchtower, rviz should reflect a faster localization of the bot. The bot appears as a red arrow in the visualization. Verify the position and orientation in rviz match the actual bot.

### Run the city rescue demo

#### Start the city-rescue version of indefinite navigation demo on the Aubotbot
For each Autobot, run
```
dts duckiebot demo --duckiebot_name <HOSTNAME> \
--demo_name indefinite_navigation \
--package_name duckietown_demos \
--image duckietown/dt-core-cityrescue:v1-arm32v7
```
* _HOSTNAME_, e.g. `autobot27`

#### Start the rescue-center
```
docker run --name rescue_center \
--network=host -it --rm \
-e ROS_MASTER_IP=http://<LAB_SERVER_IP>:11311 \
-e MAP_FORK=<YOUR_FORK> \
-e MAP_NAME=<YOUR_MAP> \
duckietown/duckie-rescue-center:v1-amd64
```
* _LAB_SERVER_IP_: e.g. `192.168.1.187`
* _YOUR_FORK_: your GitHub duckietown-world fork (where the Autolab map is created). For ETHZ AMoD Lab, the fork is `jasonhu5`
* _YOUR_MAP_: the map file name, without ".yaml" suffix. For ETHZ AMoD Lab, the map name is `ethz_amod_lab_k31`

___Expected Outcome___: Information should now be printed from the terminal window, showing e.g. which bots are now being monitored, or are they in distress? If so, what kind of distress situation they are in?

By default, no Autobot is being monitored. Place the demo bot near an offset compensated watchtower. And in a dt-ros-commons container (e.g. started with `docker run -it --rm --net host duckietown/dt-ros-commons:daffy-amd64 /bin/bash`), run `rosparam set /rescue/rescue_center/add_duckiebot <AUTOBOT_NUMBER>`
* _AUTOBOT_NUMBER_: for autobot27, it's `27`

In case one needs to stop a bot from being monitored for distress, run `rosparam set /rescue/rescue_center/remove_duckiebot <AUTOBOT_NUMBER>`

___Expected outcome___: Now the autobots are being monitored for distress. Try driving, with keyboard_control demo or dashboard, the autobot out of lane. Verify in rviz that the simple localization marker shows the bot is out of lane very fast, and the bot should begin a rescue operation immediately. The bot will first not respond to joystick commands anymore, and will move backwards, maybe with an angular velocity. Until the bot is at a position that is good for lane following, it's released from rescue nodes and starts to do lane following. This is the same for other distress situations as shown in the demo video.

## Troubleshooting

1. Why the rviz visualization does not show the autobot moving?
    * Make sure the topic `/cslam_markers` and `/simple_loc` are added
    * If you do not see online localization results, check with `dts start_gui_tools <LAB_SERVER_HOSTNAME>` and `rqt_image_view` on the images from watchtowers.
    * If you see online localizaiton results, but not simple localization results, make sure
        1. The tag_id:bot_number mapping has been added in the [build developed image](#lab-server) step.
        2. The watchtower that sees the bot is [offset compensated](#run-the-simple-localization-system)
2. Why is rescue not triggered when the bot is in distress?
    * Verify on rviz that the distress situation is reflected with localization. It is possible that the bot is not visible by watchtowers anymore. In that case, adjust watchtowers' heading for better coverage of the map.
    * Check the docker processes on the autobot for rescue-acquisition-bridge. It has to be the required image and running.
    * Make sure that the bot is added to the monitor list of rescue-center
