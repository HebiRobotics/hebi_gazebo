# hebi_gazebo

This repository holds the plugins and configuration files used to simulate HEBI robots in Gazebo.
In addition, you will need the model files contained in the hebi_description package, or your own hrdf/urdf in the case of a custom robot.
You may also want to download hebi_moveit_configs if you are interested in Moveit! integration.

These instruction have been tested on Ubuntu 18.04 (Gazebo9/ROS Melodic) and Ubuntu 16.04 (Gazebo7/ROS Kinetic)

## Usage Guide
While this package is compatible with ROS simulation methods, the HEBI Gazebo plugin is fully independent of ROS, and can be used without it. Both use cases are described below

### Simulating with ROS
1. Gazebo needs to know the path to the HEBI plugin and models. This is done by setting the GAZEBO_PLUGIN_PATH and GAZEBO_MODEL_PATH environment variables.
```
export GAZEBO_PLUGIN_PATH=${GAZEBO_PLUGIN_PATH}:path/to/your/ros_ws/src/hebi_gazebo/plugin/gazebo9
export GAZEBO_MODEL_PATH=${GAZEBO_MODEL_PATH}:/path/to/your/ros_ws/src
```
Note that the plugin path should point to the directory under hebi_gazebo/plugin/ that matches your version of Gazebo. For ROS Melodic, the default is Gazebo9, for ROS Kinetic the default is Gazebo7.

2. Make sure that `hebi_gazebo`, `hebi_description`, and `hebi_cpp_api_ros_examples` are cloned and built in your current, sourced workspace.
3. To test, run the following command:
`roslaunch hebi_gazebo arm_simulation.launch arm_type:=A-2085-06`
4. You should see the 6-DoF arm kit rendered in Gazebo. A `rostopic list` should show several topic-based interfaces for controlling the arm.
Note that the ROS nodes for simulation are identical to those used for controlling HEBI hardware.

### Simulating without ROS
NOTE: Currently the hebi_description pipeline that generates SDF files is not working properly without a minimal ROS install. Specifically, the xacro conversion process uses rospack to find hebi_description. Once the SDF file is generated, there is no direct dependency on ROS.
To simulate a robot in Gazebo without ROS, we will need to generate SDF files, so that Gazebo can model our robot. These files can be generated from the kit HRDF files using tools contained in the hebi_description package.
1. clone/download the hebi_gazebo and hebi_description packages.
2. Install xacro from pypy (https://pypi.org/project/xacro/)
3. Run the script hebi_description/scripts/generate_pipeline_batch_v1.bash
This generates SDF model folders for each arm HRDF contained in hebi_description/hrdf.
If you would like to use this script to convert your own custom HRDF, the generate_pipeline_v1.bash script can be passed a file for conversion.
4. At this point the hebi_description/models directory should contain several subdirectories, one for each processed hrdf file.
5. Gazebo needs to be told the location of the HEBI plugin and models. Run
```
export GAZEBO_PLUGIN_PATH=${GAZEBO_PLUGIN_PATH}:path/to/hebi_gazebo/plugin/gazebo9
export GAZEBO_MODEL_PATH=${GAZEBO_MODEL_PATH}:/path/to/hebi_description/models
```
Note that the plugin path should point to the directory under hebi_gazebo/plugin/ that matches your version of Gazebo.

6. You can load your model into Gazebo in one of two ways:
a. run gazebo from the terminal `gazebo`.
The second tab in the left panel menu, "Insert", will have several locations it can find models on your system.
The newly generated HEBI SDF files should appear here if your environment variables are set.
b. Write a .world file that includes the model. For an example of this, see hebi_gazebo/worlds/scara_world.world

7. At this point, your simulated robot can be interacted with through the standard HEBI tools such as Scope or any of the programmatic APIs.
