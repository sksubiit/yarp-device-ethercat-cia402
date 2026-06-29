Motor Setup — Installation & Configuration

Overview

This document describes the steps required to install and configure the "Motor" setup, including adding library paths, updating the linker cache, giving `yarprobotinterface` the necessary network capabilities, and running the interface.

Prerequisites

- A built Robotology / YARP workspace.
- ROS 2 installed for your target distribution.
- The repositories referenced below (build instructions are in their READMEs).

1. Install repository and dependencies

Follow the installation instructions in the device repository:

- Prerequisites & build steps: https://github.com/gbionics/yarp-device-ethercat-cia402
- Robotology superbuild (example / reference): https://github.com/robotology/robotology-superbuild

Ensure your Robotology/YARP workspace and ROS 2 are built and installed before continuing.

2. Add ROS 2 library path

Replace `<distro>` with your ROS distribution (for example, `jazzy`) and run:

```bash
echo "/opt/ros/<distro>/lib" | sudo tee /etc/ld.so.conf.d/ros-<distro>.conf
```

3. Add Robotology / YARP workspace install libs

Replace the example path below with your workspace install `lib` folder, for example the `install/lib` directory produced by the Robotology superbuild:

```bash
echo "/home/youruser/development/robotology-superbuild/build/install/lib" | sudo tee /etc/ld.so.conf.d/robotology-workspace.conf
```

4. Update linker cache

```bash
sudo ldconfig
```

5. Give `yarprobotinterface` the required network capabilities

This is necessary to allow raw network access when running without root:

```bash
sudo setcap cap_net_raw,cap_net_admin+ep "$(which yarprobotinterface)"
```

Run sequence

1. Start the YARP name server in the background:

```bash
yarpserver &
```

2. Launch `yarprobotinterface` with your configuration file:

```bash
yarprobotinterface --config /path/to/your/yarprobotinterface_config.ini
```

After these steps, `yarprobotinterface` should be able to find NWS plugins and ROS 2 libraries even when capabilities are set.

Troubleshooting

- Missing .so errors:
  - Verify the correct paths were added in `/etc/ld.so.conf.d/`.
  - Re-run `sudo ldconfig`.
  - Confirm the `install/lib` paths actually contain the required `.so` files.
- Check capabilities on `yarprobotinterface`:

```bash
getcap "$(which yarprobotinterface)"
```

References

- yarp-device-ethercat-cia402: https://github.com/gbionics/yarp-device-ethercat-cia402
- robotology-superbuild: https://github.com/robotology/robotology-superbuild
- SOEM: https://github.com/OpenEtherCATsociety/SOEM