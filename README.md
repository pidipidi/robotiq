# robotiq
* Based on the `jade-devel` branch of [`robotiq`](https://github.com/ros-industrial/robotiq), we have built [our own fork with the `irg-jade` branch](https://github.com/Shentheman/robotiq/tree/irg-jade).

## Meshes
* We are using the original URDF [`robotiq/robotiq_s_model_visualization/cfg/robotiq_hand_macro.urdf.xacro`](https://github.com/ros-industrial/robotiq/blob/jade-devel/robotiq_s_model_visualization/cfg/robotiq_hand_macro.urdf.xacro) from [`jade-devel` branch of `robotiq`](https://github.com/ros-industrial/robotiq)


## Gazebo simulation pipeline
* `roslaunch robotiq_s_model_articulated_gazebo robotiq_gripper_empty_world.launch`
* [`libRobotiqHandPlugin_controlMsg.so`](https://github.com/Shentheman/robotiq/blob/16a39a78a2db1c43830b7d025a1fc3901d985deb/robotiq_s_model_visualization/cfg/common.gazebo.xacro#L11) here will publish the joint states of the gripper to `/left_hand/joint_states`
      - This `/left_hand/joint_states` is defined [here](https://github.com/Shentheman/robotiq/blob/16a39a78a2db1c43830b7d025a1fc3901d985deb/robotiq_s_model_articulated_gazebo_plugins/src/RobotiqHandPlugin_controlMsg.cpp#L68)
* And then the [`joint_state_publisher`](https://github.com/Shentheman/robotiq/blob/16a39a78a2db1c43830b7d025a1fc3901d985deb/robotiq_s_model_articulated_gazebo/launch/controller_utils.launch#L12) will publish the information at `/left_hand/joint_states` inside the `\source_list` forward to `/joint_states`.
* Then the [`robot_state_publisher`](https://github.com/Shentheman/robotiq/blob/16a39a78a2db1c43830b7d025a1fc3901d985deb/robotiq_s_model_articulated_gazebo/launch/controller_utils.launch#L21) will use the information at `/joint_states` to publish the `tf`, so in RVIZ, you can view the robot using `RobotModel`.


## Usage
* http://gazebosim.org/tutorials?tut=drcsim_robotiq_hand&cat=
* `roslaunch robotiq_s_model_articulated_gazebo robotiq_gripper_empty_world.launch`
* To close it half-way
  * If we are using the [`RobotiqHandPlugin` plugin](https://github.com/Shentheman/robotiq/blob/irg-jade/robotiq_s_model_articulated_gazebo_plugins/src/RobotiqHandPlugin.cpp)
    * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,127,255,0,155,0,0,255,0,0,0,0,0}`
    * all the way
      * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,255,255,0,155,0,0,255,0,0,0,0,0}`
  * If we are using the [`RobotiqHandPlugin_controlMsg` plugin](https://github.com/Shentheman/robotiq/blob/irg-jade/robotiq_s_model_articulated_gazebo_plugins/src/RobotiqHandPlugin_controlMsg.cpp)
    * `rostopic pub --once /left_hand/SModelRobotOutput robotiq_s_model_control/SModel_robot_output {1,0,1,0,0,0,127,255,0,155,0,0,255,0,0,0,0,0,0}`
    * all the way
      * `rostopic pub --once /left_hand/SModelRobotOutput robotiq_s_model_control/SModel_robot_output {1,0,1,0,0,0,127,255,0,255,0,0,255,0,0,0,0,0,0}`

* To fully open it
  * If we are using the [`RobotiqHandPlugin` plugin](https://github.com/Shentheman/robotiq/blob/irg-jade/robotiq_s_model_articulated_gazebo_plugins/src/RobotiqHandPlugin.cpp)
    * `rostopic pub --once left_hand/command robotiq_s_model_articulated_msgs/SModelRobotOutput {1,0,1,0,0,0,0,0,0,155,0,0,0,0,0,0,0,0}`
  * If we are using the [`RobotiqHandPlugin_controlMsg` plugin](https://github.com/Shentheman/robotiq/blob/irg-jade/robotiq_s_model_articulated_gazebo_plugins/src/RobotiqHandPlugin_controlMsg.cpp)
    * `rostopic pub --once /left_hand/SModelRobotOutput robotiq_s_model_control/SModel_robot_output {1,0,1,0,0,0,127,0,0,0,0,0,0,0,0,0,0,0,0}`


## Supports for Gazebo 7 on indigo
* You have to add `set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")` to `CMakeLists.txt`
* Then for the Gazebo plugin `c++` files, you might have to change `const` to `constexpr`. Otherwise it won't compile.
  - https://github.com/rogerclarkmelbourne/Arduino_STM32/issues/402
