### Configuration for the Dual-Motor Independent Network Setup

#### System Architecture: Dual-Network Master
This setup enables the control of two motors without using a physical EtherCAT daisy-chain topology (single cable from Motor 1 to Motor 2). Instead, each motor is connected directly to its own Network Interface Card (NIC), and the two networks are fused in software.

#### Physical Topology
- **Port 1 (e.g., eth0):** Connected via a 594 cable to Motor 1.
- **Port 2 (e.g., eth1):** Connected via a 594 cable to Motor 2.

The configuration is organized into three distinct levels within the `yarprobotinterface` XML system:

1.  **Level 1 (Hardware Drivers):** Two instances of the `CiA402MotionControl` device, each bound to its respective NIC (`ifname`).
2.  **Level 2 (Logical Remapper):** A `controlBoardRemapper` device that "attaches" to both hardware drivers. It creates a unified 2-joint list (joint1, joint2), hiding the dual-network complexity from the higher layers.
3.  **Level 3 (Network Wrappers):** The `controlBoard_nws_ros2` and `controlBoard_nws_yarp` attach to the Remapper, exposing a single unified interface for ROS 2 topics and the `yarpmotorgui`.

#### Synchronization and Feedback
In order to ensure coherent timeline across both networks, satisfying the synchronization requirements of the YARP/ROS middle-ware, a patch was applied to the `CiA402MotionControl.cpp` driver to use the PC System Clock (`yarp::os::Time::now()`) for all feedback timestamps. Without this patch, the driver may fail during initialization with timestamp-related errors when loading this configuration file. 

#### Important Considerations
- In multi-motor chains proper synchronization between drives is critical. Use Distributed Clock (DC) to ensure deterministic timing and aligned control loops.
- See Synapticon docs on Synchronization via Distributed Clock: https://doc.synapticon.com/circulo/sw5.1/motion_control/advanced_control_options/distributed_clocks.html?Highlight=Synchronization%20via%20Distributed%20Clock

