# robotiq
## Gazebo
* http://gazebosim.org/tutorials?tut=drcsim_robotiq_hand&cat=
  * To launch the simulated gripper controller
    * `$ roslaunch robotiq_s_model_articulated_gazebo robotiq_gripper_empty_world.launch`
  * To control the simulated gripper via sending command message to `left_hand/command`
    * To close it half-way
      * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,127,255,0,155,0,0,255,0,0,0,0,0}`
    * To fully open it
      * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,0,255,0,155,0,0,255,0,0,0,0,0}`
    * You can remap the topic which takes the commands in `./src/robotiq/robotiq_s_model_articulated_gazebo/launch/robotiq_gripper_empty_world.launch`
      * e.g. `<remap from="joint_states" to="/robotiq_hands/left_hand/joint_states"/>`
  * When simulating using `robotiq_gripper_empty_world.launch`, we are distinguishing the left hand and right hand. So we are using the plugin `./src/robotiq/robotiq_s_model_articulated_gazebo_plugins/include/robotiq_s_model_articulated_gazebo_plugins/RobotiqHandPlugin.h` developed for both hands of *atlas*. However, when we are simulating the robotiq gripper within our Care-O-Bot (raw3-6), we have only 1 gripper. So we develop a separate Gazebo plugin, `./src/robotiq/robotiq_s_model_articulated_gazebo_plugins/include/robotiq_s_model_articulated_gazebo_plugins/RobotiqHandPluginSingleHand.h`. By using this plugin, the topics that the plugin listens commands from and the topics that the plugin publishes joint states to are consistent with our real robitiq gripper driver:
    * `/SModelRobotOutput` == `/<left or right>_hand/command`
    * `/SModelRobotInput` == `/<left or right>_hand/state`
