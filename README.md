# robotiq
## Gazebo
* http://gazebosim.org/tutorials?tut=drcsim_robotiq_hand&cat=
  * To launch the simulated gripper controller
    * `$ roslaunch robotiq_s_model_articulated_gazebo robotiq_gripper_empty_world.launch`
  * To control the simulated gripper via sending command message to `left_hand/command`
    * To close it half-way
      * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,127,255,0,155,0,0,255,0,0,0,0,0}`
      * For cob: `rostopic pub --once /SModelRobotOutput robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,127,255,0,155,0,0,255,0,0,0,0,0}`

    * To fully open it
      * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,0,255,0,155,0,0,255,0,0,0,0,0}`
    * You can remap the topic which takes the commands in `./src/robotiq/robotiq_s_model_articulated_gazebo/launch/robotiq_gripper_empty_world.launch`
      * e.g. `<remap from="joint_states" to="/robotiq_hands/left_hand/joint_states"/>`
  * When simulating using `robotiq_gripper_empty_world.launch`, we are distinguishing the left hand and right hand. So we are using the plugin `./src/robotiq/robotiq_s_model_articulated_gazebo_plugins/include/robotiq_s_model_articulated_gazebo_plugins/RobotiqHandPlugin.h` developed for both hands of *atlas*. However, when we are simulating the robotiq gripper within our Care-O-Bot (raw3-6), we have only 1 gripper. So we develop a separate Gazebo plugin, `./src/robotiq/robotiq_s_model_articulated_gazebo_plugins/include/robotiq_s_model_articulated_gazebo_plugins/RobotiqHandPluginSingleHand.h`. By using this plugin, the topics that the plugin listens commands from and the topics that the plugin publishes joint states to are consistent with our real robitiq gripper driver:
    * `/SModelRobotOutput` == `/<left or right>_hand/command`
    * `/SModelRobotInput` == `/<left or right>_hand/state`

## Issue
* Note that when running the real gripper, we need to run the `robotiq_joint_state_publisher` from `./src/cob_robots/cob_bringup/drivers/robotiq.launch` to publish the joint states of the gripper to `/SModelRobotInput`. Therefore, when running the simulated gripper in Gazebo, we want to keep the same code which publishes joint states of the gripper from the `robotiq_joint_state_publisher`. We have to change our Gazebo plugin - `RobotiqHandPluginSingleHand.h` at `./src/robotiq/robotiq_s_model_articulated_gazebo_plugins/include/robotiq_s_model_articulated_gazebo_plugins` so that the plugin will not publish same joint states to `/SModelRobotInput`. If both nodes are publishing joint states to the same topic - `/SModelRobotInput`, the content under that topic will jitter, e.g. `1` in this moment, `2` in next moment, and `1` in next next moment ...

