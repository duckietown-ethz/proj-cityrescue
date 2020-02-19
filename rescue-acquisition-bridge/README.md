# Proj-city-rescue version of acquisition-bridge

## System explanation

In an [Autolab](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_definition.html), because the lab server machine(s) and the [Autobots](https://docs.duckietown.org/daffy/opmanual_autolab/out/autolab_autobot_specs.html) are running on different ROS masters, an acquisition-bridge is used to pass messages between the different ROS masters.

In addition to the acquisition-bridge that this repository is forked from, project CityRescue requires a few more data channels between the lab server machine and the Autobots, namely:

* Rescue trigger (Server -> Autobot)
    * virtually shared topic `/<AUTOBOT_NAME>/recovery_mode`
    * message type: `BoolStamped` in `duckietown_msgs.msg`
* Rescue car commands (Server -> Autobot)
    * virtually shared topic `/<AUTOBOT_NAME>/lane_recovery_node/car_cmd`
    * message type: `Twist2DStamped` in `duckietown_msgs.msg`
* Autobot fsm state (Autobot -> Server)
    * the server subscribe to `/<AUTOBOT_NAME>/fsm_mode`
    * Autobot(s) publishes to `/<AUTOBOT_NAME>/fsm_node/mode`
    * message type: `FSMState` in `duckietown_msgs.msg`

To clarify, the ___virtually shared topic___ statement just means the topic names on the server rosmaster and the Autobot rosmaster are the same.

## Commands to build and run

To build:
```
dts devel build -f --arch arm32v7 -H <HOSTNAME>.local
```

To launch on duckiebot:
```
docker -H <HOSTNAME>.local run --name rescue-acquisition-bridge --rm --network=host -v /data:/data -e LAB_ROS_MASTER_IP=<LAB_SERVER_IP> -dit duckietown/rescue-acquisition-bridge:daffy-arm32v7
```