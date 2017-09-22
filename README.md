# robotiq
## Gazebo
* Please use released version 1.1.10
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
