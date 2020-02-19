# City Rescue Enabled dt-core

## System explanation

This repository builds a variant of `dt-core` image. It adds the definition of the state transitions into and out of ___LANE_RECOVERY___ state to the FSM. When the bot is triggered to go into LANE_RECOVERY state, it only executes the commands from topic `/<AUTOBOT_NAME>/lane_recovery_node/car_cmd`. This infrastructure is needed so that when a rescue operation is needed, the bot will only follow commands from that topic, which is used by the [Rescue center and rescue agents](https://github.com/jasonhu5/duckie-rescue-center/tree/v1).

## Commands to build and run

To build:
```
dts devel build -f --arch arm32v7 -H <HOSTNAME>.local
```

To run:
```
dts duckiebot demo --duckiebot_name <HOSTNAME> --demo_name indefinite_navigation --package_name duckietown_demos --image duckietown/dt-core-cityrescue:v1-arm32v7
