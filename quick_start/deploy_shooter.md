# rm_shooter_controller部署方案

## shooter的urdf编写
### trigger_joint的力矩、速度
#### 确定最大力矩、速度
拨弹轮的最大转速=电机的转速*传动比
拨弹轮的最大力矩=电机的扭矩*传动比
电机的功率P=FV
减速比可以使电机在相同功率下减小输出的转速，进而输出更大的力矩
#### 在urdf中实现
dynamicx示例:
```xacro
<limit effort="3.0" velocity="43.54" lower="-1e16" upper="1e16"/>
```

### 电机的传动比(拨弹:电机）
传动比不同兵种、同个兵种不同赛季都可能会有不同，这项数据需要向机械组要
22赛季dynamicx各兵种传动比:
 英雄：4:1
 哨兵：1:1
 步兵：2.5:1
 无人机：2.5:1
 
### 左右摩擦轮
#### 数据获取
左右摩擦轮与pitch轴连接，向机械组要其偏移量填入
#### 在urdf中实现
dynamicx示例:
```xacro
<xacro:friction_wheel prefixs="left" connected_to="pitch"
                          wheel_x_offset="${wheel_offset_x}" wheel_y_offset="${wheel_offset_y}"
                          wheel_z_offset="${wheel_offset_z}"/>

<xacro:friction_wheel prefixs="right" connected_to="pitch"
                          wheel_x_offset="${wheel_offset_x}" wheel_y_offset="${-wheel_offset_y}"
                          wheel_z_offset="${wheel_offset_z}"/>
```

### 扳机link
#### 数据获取
向机械组索要trigger的stl文件以及相关参数(包含质量、质心、以及原点处和质心处的惯性矩阵)填入
#### 在urdf中实现
dyanmicx示例:
```xacro
<inertial>
    <mass value="1.4"/>
    <origin xyz="-0.0146 -0.00736 -0.00154"/>
    <inertia ixx="4.443e-3" ixy="-1.694e-3" ixz="-1.776e-3" iyy="1.122e-2"
             iyz="-3.693e-4" izz="1.347e-2"/>
</inertial>
```
### trigger_joint偏移量
#### 数据获取
trigger_joint的子link是trigger，父link是base_link，向机械索要trigger到base_link的偏移量填入
#### 在urdf中实现
dynamicx示例:
```xacro
<origin xyz="0 0 0.063" rpy="0 0 0"/>
```

## shooter_controller中的重要参数
push_per_rotation:拨弹轮每旋转一圈发射的子弹数
每个拨弹轮都不同，这个参数需要实时调整

block_effort:判断拨弹轮阻塞的力矩
扳机的实时力矩受到电机和其传动比控制，根据block_effort=电机堵转扭矩*传动比可以进行初步估算

## 实车调试
1.usb2can连接或远程连接nuc
2.运行rm_hw中的launch
*mon launch rm_hw rm_hw.launch*
3.加载shooter_controller
*mon launch rm_shooter_controllers load_controllers.launch*
4.运行rqt，在rqt中调整mode以及hz进行调试
*rqt*











