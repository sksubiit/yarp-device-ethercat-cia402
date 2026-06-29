### Configuration for the Two-Motor Drive-to-Drive Setup

This template controls two EtherCAT CiA402 drives on the same physical EtherCAT chain.
It is the drive-to-drive alternative to `template_2_motor`, which uses two independent
network interfaces and a software remapper.

#### Physical Topology

- PC EtherCAT NIC -> Drive 1 EtherCAT IN
- Drive 1 EtherCAT OUT -> Drive 2 EtherCAT IN

The YARP device is one `CiA402MotionControl` instance with `num_axes=2`. Axis `joint1`
maps to EtherCAT slave 1 and axis `joint2` maps to EtherCAT slave 2. If the axes appear
swapped, check the cable order and the discovered slave order.

#### Files

- `config.xml`: loads the two-axis hardware device and the YARP/ROS 2 wrappers.
- `motion_control/all_joint_mc.xml`: main EtherCAT hardware configuration.
- `motion_control/all_joint_mc_nws.xml`: exposes `/template_2_motor_d2d/motor` for `yarpmotorgui`.
- `yarpmotorgui.ini`: GUI configuration for the `motor` part.

#### Before Running

Edit `motion_control/all_joint_mc.xml` and set:

- `ifname`: the NIC connected to Drive 1.
- `inverted_motion_sense_direction`: set each axis to `true` if its positive direction is reversed.
- `pos_limit_min_deg` / `pos_limit_max_deg`: physical limits for each motor or joint.
- `simple_pid_*`: gains for each axis, or remove both gain lines if you do not want the template to program them.

Distributed Clock is enabled by default because both drives are on the same EtherCAT chain.
If your drives or firmware do not support DC cleanly, set `enable_dc` to `false` for bring-up.

#### Run

Start the YARP name server:

```bash
yarpserver
```

In another terminal, start the robot interface:

```bash
yarprobotinterface --config development/yarp-device-ethercat-cia402/config/robot/template_2_motor_d2d/config.xml
```

Then open the motor GUI:

```bash
yarpmotorgui --from development/yarp-device-ethercat-cia402/config/robot/template_2_motor_d2d/yarpmotorgui.ini
```
