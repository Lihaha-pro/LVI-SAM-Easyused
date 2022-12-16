# LVI-SAM-Easyused

This repository contains modified code of [LVI-SAM](https://github.com/TixiaoShan/LVI-SAM) for easier using. 

---



## Compile

You can use the following commands to download and compile the package.

```shell
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
git clone https://github.com/Cc19245/LVI-SAM-Easyused
cd ..
catkin_make
```

**Note**：If you want to use the no-modified code (origin LVI-SAM official code), you can change the defination in `CMakeLists.txt` and compile again.

```cmake
################## 编译开关 compile switch##############
# -DIF_OFFICIAL=1: use origin official LVI-SAM code
# -DIF_OFFICIAL=0: use modified code of this repo
add_definitions(-DIF_OFFICIAL=0)
```

---



## Params config

1. `params_camera.yaml`: set the VIO params, especially for $T\_imu\_camera$. It's same as VINS-Mono.

```yaml
###################### extrinsic between IMU and Camera  ###########################
###################### T_IMU_Camera, Camera -> IMU       ###########################  
# R_imu_camera
extrinsicRotation: !!opencv-matrix
   rows: 3
   cols: 3
   dt: d
   data: [ 0,    0,    -1, 
               -1,     0,    0, 
                0,     1,    0]
# t_imu_camera
extrinsicTranslation: !!opencv-matrix
   rows: 3
   cols: 1
   dt: d
   data: [0.006422381632411965, 0.019939800449065116, 0.03364235163589248]
```

2. `params_lidar.yaml`: set the LIO params, especially for $T\_imu\_lidar$. 

```yaml
  ###################### extrinsic between IMU and LiDAR  ###########################
  ###################### T_IMU_LiDAR, LiDAR -> IMU       ############################
  # t_imu_lidar
  extrinsicTranslation: [0.0,   0.0,   0.0]    
  # R_imu_lidar
  extrinsicRotation: [-1,   0,    0, 
                                         0,    1,    0, 
                                         0,    0,   -1]
```

**Besides**, due to the special IMU (the Euler angle coordinate system is different from the angular velocity and angular velocity coordinate system) of official dataset , you also need to set which axis the IMU rotates around counterclockwise to get a positive output. For official sensor equipment, it is set as follows.

```yaml
  ## 对绝大多数IMU来说，下面三个值分别是"+z", "+y", "+x" (for most of IMUs, the following config is "+z", "+y", "+x")
  # 绕着哪个轴逆时针转动，输出yaw角度为正(which axis the IMU rotates around counterclockwise to get a positive yaw angle)
  yawAxis: "-z"  
  # 绕着哪个轴逆时针转动，输出pitch角度为正(which axis the IMU rotates counterclockwise to get a positive pitch angle)
  pitchAxis: "+x"    
  # 绕着哪个轴逆时针转动，输出roll角度为正(which axis the IMU rotates around counterclockwise to get a positive roll angle)
  rollAxis: "+y"    
```

<p align='center'>
    <img src="./doc/official-equipment.png" alt="drawing" width="800"/>
</p>

**However**, for most of the IMUs, the Euler angle coordinate system is same as the angular velocity and angular velocity coordinate system. So the above parameters should be set as follows.

```yaml
  ## 对绝大多数IMU来说，下面三个值分别是"+z", "+y", "+x" (for most of IMUs, the following config is "+z", "+y", "+x")
  # 绕着哪个轴逆时针转动，输出yaw角度为正(which axis the IMU rotates around counterclockwise to get a positive yaw angle)
  yawAxis: "+z"  
  # 绕着哪个轴逆时针转动，输出pitch角度为正(which axis the IMU rotates counterclockwise to get a positive pitch angle)
  pitchAxis: "+y"    
  # 绕着哪个轴逆时针转动，输出roll角度为正(which axis the IMU rotates around counterclockwise to get a positive roll angle)
  rollAxis: "+x"    
```

---



## Run the package on different datasets

1. [Official dataset](https://drive.google.com/drive/folders/1q2NZnsgNmezFemoxhHnrDnp1JV_bqrgV)

   - Run the launch file:

     ```
     roslaunch lvi_sam run.launch
     ```

     **Note**: If you want to test the origin official LVI-SAM code (e.g. set `add_definitions(-DIF_OFFICIAL=1)` in CMakeLists.txt to compile), you should run launch file as following.

     ```
     roslaunch lvi_sam run_official.launch
     ```

   - Play existing bag files, e.g. handheld.bag:

     ```
     rosbag play handheld.bag 
     ```

   - Results of origin official code (up fig) and our's modified code (down fig) on handheld.bag:

     <p align='center'>
         <img src="./doc/handheld-official.png" alt="drawing" width="600"/>
     </p>

     <p align='center'>
         <img src="./doc/handheld.png" alt="drawing" width="600"/>
     </p>

2. [M2DGR dataset](https://github.com/SJTU-ViSYS/M2DGR)

   - Run the launch file:

     ```
     roslaunch lvi_sam M2DGR.launch
     ```

   - Play existing bag files, e.g. gate_01.bag:

     ```
     rosbag play gate_01.bag 
     ```

   - Results of our's modified code on gate_01.bag:

     <p align='center'>
         <img src="./doc/gate_01.png" alt="drawing" width="600"/>
     </p>

3. [My test dataset](https://1drv.ms/u/s!AqYajE_ft9lwg0paJQu_DRzU-GQ5?e=A95yfn)

   - Run the launch file:

     ```
     roslaunch lvi_sam backbag.launch
     ```

   - Play existing bag files, e.g. backbag.bag:

     ```
     rosbag play backbag.bag 
     ```

   - Results of our's modified code on backbag.bag:

     <p align='center'>
         <img src="./doc/backbag.png" alt="drawing" width="600"/>
     </p>

   - Results of our's modified code on 0117-1525.bag (Due to privacy issues, this data package is not open source):

     ```
     roslaunch lvi_sam ljj.launch
     rosbag play 0117-1525.bag 
     ```

     <p align='center'>    <img src="./doc/ljj.png" alt="drawing" width="600"/></p>

---



## TODO

  - [ ] More test on different dataset, e.g. KITTI, KAIST. **However**, these datasets' lidar data have no **ring** information. So LVI-SAM can't run directly. If you want to run on these datasets, you need to modifidy the code to add this information refer to [LeGO-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM).

---



## Notes

If you want to know what changes I made and why they make sense, you can refer to my blog: [LVI-SAM坐标系外参分析与代码修改，以适配各种数据集](https://blog.csdn.net/qq_42731705/article/details/128344179).

---



## Acknowledgement

- Origin official [LVI-SAM](https://github.com/TixiaoShan/LVI-SAM).