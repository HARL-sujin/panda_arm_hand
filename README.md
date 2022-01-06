# panda_arm_hand
# panda_arm_hand.urdf.xacro
<?xml version='1.0' encoding='utf-8'?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="panda">

  <!-- Name of this panda -->
  <xacro:arg name="arm_id" default="panda" />
  <!-- Should a franka_gripper be mounted at the flange?" -->
  <xacro:arg name="hand" default="true" />
  <!-- Is the robot being simulated in gazebo?" -->
  <xacro:arg name="gazebo" default="false" />

  <xacro:unless value="$(arg gazebo)">

    <!-- Create a URDF for a real hardware -->
    <xacro:include filename="$(find franka_description)/robots/panda_arm.xacro" />
    <xacro:panda_arm arm_id="$(arg arm_id)" safety_distance="0.03"/>

    <xacro:if value="$(arg hand)">
      <xacro:include filename="$(find franka_description)/robots/hand.xacro"/>
      <xacro:hand ns="$(arg arm_id)" rpy="0 0 ${-pi/4}" connected_to="$(arg arm_id)_link8" safety_distance="0.03"/>
    </xacro:if>
  </xacro:unless>

  <xacro:if value="$(arg gazebo)">

    <xacro:arg name="xyz" default="0 0 0" />
    <xacro:arg name="rpy" default="0 0 0" />

 <!-- arm with gripper -->
  <xacro:panda_arm arm_id="$(arg arm_id)" connected_to="base"  xyz="0 -0.5 1" safety_distance="0.03"/>
  <xacro:hand ns="$(arg arm_id)" rpy="0 0 ${-pi/4}" connected_to="$(arg arm_id)_link8" safety_distance="0.03"/>


    <!-- Create a simulatable URDF -->
    <xacro:include filename="$(find franka_description)/robots/utils.xacro" />
    <xacro:include filename="$(find franka_description)/robots/panda_gazebo.xacro" />

    <xacro:panda_arm arm_id="$(arg arm_id)" />

    <xacro:if value="$(arg hand)">
      <xacro:hand ns="$(arg arm_id)" rpy="0 0 ${-pi/4}" connected_to="$(arg arm_id)_link8" />
      <xacro:gazebo-joint joint="$(arg arm_id)_finger_joint1" transmission="hardware_interface/EffortJointInterface" />
      <xacro:gazebo-joint joint="$(arg arm_id)_finger_joint2" transmission="hardware_interface/EffortJointInterface" />
    </xacro:if>

    <!-- Gazebo requires a joint to a link called "world" for statically mounted robots -->
    <link name="world" />
    <joint name="world_joint" type="fixed">
      <origin xyz="$(arg xyz)" rpy="$(arg rpy)" />
      <parent link="world" />
      <child  link="$(arg arm_id)_link0" />
    </joint>


    <xacro:gazebo-joint joint="$(arg arm_id)_joint1" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint2" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint3" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint4" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint5" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint6" transmission="hardware_interface/EffortJointInterface" />
    <xacro:gazebo-joint joint="$(arg arm_id)_joint7" transmission="hardware_interface/EffortJointInterface" />

    <xacro:transmission-franka-state arm_id="$(arg arm_id)" />
    <xacro:transmission-franka-model arm_id="$(arg arm_id)"
       root="$(arg arm_id)_joint1"
       tip="$(arg arm_id)_joint8"
     />

    <gazebo>
      <plugin name="gazebo_ros_control" filename="libgazebo_ros_control.so">
        <controlPeriod>0.001</controlPeriod>
        <robotSimType>franka_gazebo/FrankaHWSim</robotSimType>
      </plugin>
      <self_collide>true</self_collide>
    </gazebo>
  </xacro:if>

</robot>
