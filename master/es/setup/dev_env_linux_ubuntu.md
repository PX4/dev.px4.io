# Entorno de desarrollo en Ubuntu LTS / Debian Linux

[Ubuntu Linux LTS](https://wiki.ubuntu.com/LTS) 16.04 is the standard/preferred Linux development OS. It allows you to build for [most PX4 targets](../setup/dev_env.md#supported-targets) (NuttX based hardware, Qualcomm Snapdragon Flight hardware, Linux-based hardware, Simulation).

> **Note** Ubuntu 18.04 is required if you want to work with *ROS Melodic* (which does not install on Ubuntu 16.04).

Las siguientes instrucciones explican cómo configurar *manualmente* un entorno de desarrollo para cada uno de los objetivos compatibles.

> **Tip** We recommend that you use the [Convenience bash scripts](#convenience-bash-scripts) to install the Simulators and/or NuttX toolchain (this is easier than typing in the instructions below). Then follow just the additional instructions for other targets (e.g. Qualcomm Snapdragon Flight, Bebop, Raspberry Pi, etc.)

<span></span>

> **Tip** After setting up the build/simulation toolchain, see [Additional Tools](../setup/generic_dev_tools.md) for information about other useful tools.

## Scripts de Bash

Hemos creado una serie de scripts de bash que se pueden utilizar para instalar los simuladores y/o toolchain de NuttX. Todos los scripts instalan *Qt Creator IDE*, [Ninja Build System](#ninja-build-system), [dependencias comunes](#common-dependencies), [FastRTPS](#fastrtps-installation) y también descargan el código fuente de PX4 en tu ordenador (**~/src/Firmware**).

> **Tip** The scripts have been tested on clean Ubuntu 16.04 and 18.04 LTS installations. They *may* not work as expected if installed on top of an existing system or a different Ubuntu release.

Los scripts son:

* **<a href="https://raw.githubusercontent.com/PX4/Devguide/{{ book.px4_version }}/build_scripts/ubuntu_sim_common_deps.sh" target="_blank" download>ubuntu_sim_common_deps.sh</a>**: [Common Dependencies](#common-dependencies), [jMAVSim](#jmavsim) simulator
  
  * Este script contiene las dependencias comunes para todos los objetivos de compilación de PX4. Es automáticamente descargado y se ejecuta cuando se llama a cualquiera de los otros scripts.
  * Puedes ejecutar este antes de instalar las dependencias restantes para [Qualcomm Snapdragon Flight](#snapdragon-flight) o [Raspberry Pi/Parrot Bebop](#raspberry-pi-hardware).

* **<a href="https://raw.githubusercontent.com/PX4/Devguide/{{ book.px4_version }}/build_scripts/ubuntu_sim.sh" target="_blank" download>ubuntu_sim.sh</a>**: **ubuntu_sim_common_deps.sh** + [Gazebo8](#gazebo) simulator.

* **<a href="https://raw.githubusercontent.com/PX4/Devguide/{{ book.px4_version }}/build_scripts/ubuntu_sim_nuttx.sh" target="_blank" download>ubuntu_sim_nuttx.sh</a>**: **ubuntu_sim.sh** + herramientas de NuttX. 
  * *Requiere reiniciar el ordenador completamente.*
* **<a href="https://raw.githubusercontent.com/PX4/Devguide/{{ book.px4_version }}/build_scripts/ubuntu_sim_ros_melodic.sh" target="_blank" download>ubuntu_sim_ros_melodic.sh</a>**: **ubuntu_sim_common_deps.sh** + [ROS/Gazebo y MAVROS](#rosgazebo). 
  * ROS Melodic is installed with Gazebo 9 by default.
  * Catkin (ROS build system) workspace es creado en **~/catkin_ws/**.

### Cómo usar los scripts

Para usar los scripts:

1. Hacer al usuario miembro del grupo "dialout" (esto solo hay que hacerlo una vez): 
  1. Abrir una terminal e introducir el siguiente comando: 
        sh
          sudo usermod -a -G dialout $USER
  
  2. Cierra sesión y accede de nuevo (el cambio sólo es efectivo después de un nuevo inicio de sesión).
2. Descarga el script deseado
3. Ejecuta el script en un shell bash (por ejemplo para ejecutar **ubuntu_sim.sh**): 
      bash
       source ubuntu_sim.sh Reconozca cualquier mensaje a medida que progresen los scripts.

## Configuración de permisos

> **Warning** Never ever fix permission problems by using `sudo`. It will create more permission problems in the process and require a system re-installation to fix them.

El usuario necesita ser parte del grupo "dialout":

```sh
sudo usermod -a -G dialout $USER
```

Cierra sesión y accede de nuevo (el cambio sólo es efectivo después de un nuevo inicio de sesión).

## Eliminar el modemmanager

Ubuntu incluye un serial modem manager que interfiere agresivamente con cualquier uso de un puerto serie \(o serial USB\). Puede ser eliminado/desinstalado sin efectos secundarios:

```sh
sudo apt-get remove modemmanager
```

## Dependencias comunes

Actualiza la lista de paquetes e instala las siguientes dependencias para todos los objetivos de compilación de PX4.

```sh
sudo apt-get update -y
sudo apt-get install git zip qtcreator cmake \
    build-essential genromfs ninja-build exiftool -y

# Install xxd (package depends on version)
which xxd || sudo apt install xxd -y || sudo apt-get install vim-common --no-install-recommends -y

# Required python packages
sudo apt-get install python-argparse \
    python-empy python-toml python-numpy python-yaml \
    python-dev python-pip -y
sudo -H pip install --upgrade pip 
sudo -H pip install pandas jinja2 pyserial cerberus
```

Podrías también desear instalar [pyulog](https://github.com/PX4/pyulog#pyulog). Este es un útil paquete de python que contiene scripts para analizar archivos *ULog* y mostrar su contenido.

    # herramientas optionales de python
    sudo -H pip install pyulog
    

<!-- import docs ninja build system --> {% include "_ninja_build_system.md" %}

## Instalación de FastRTPS

# Install FastRTPS 1.7.1 and FastCDR-1.0.8

fastrtps_dir=$HOME/eProsima_FastRTPS-1.7.1-Linux echo "Installing FastRTPS to: $fastrtps_dir" if [ -d "$fastrtps_dir" ] then echo " FastRTPS already installed." else pushd . cd ~

      cpucores=$(( $(lscpu | grep Core.*per.*socket | awk -F: '{print $2}') * $(lscpu | grep Socket\(s\) | awk -F: '{print $2}') ))
    
      popd
    

fi

[eProsima Fast RTPS](http://eprosima-fast-rtps.readthedocs.io/en/latest/) is a C++ implementation of the RTPS (Real Time Publish Subscribe) protocol. FastRTPS is used, via the [RTPS/ROS2 Interface: PX4-FastRTPS Bridge](../middleware/micrortps.md), to allow PX4 uORB topics to be shared with offboard components.

The following instructions can be used to install the FastRTPS 1.7.1 binaries to your home directory.

```sh
wget https://www.eprosima.com/index.php/component/ars/repository/eprosima-fast-rtps/eprosima-fast-rtps-1-7-1/eprosima_fastrtps-1-7-1-linux-tar-gz -O eprosima_fastrtps-1-7-1-linux.tar.gz
tar -xzf eprosima_fastrtps-1-7-1-linux.tar.gz eProsima_FastRTPS-1.7.1-Linux/
tar -xzf eprosima_fastrtps-1-7-1-linux.tar.gz requiredcomponents
tar -xzf requiredcomponents/eProsima_FastCDR-1.0.8-Linux.tar.gz
```

> **Note** In the following lines where we compile the FastCDR and FastRTPS libraries, the `make` command is issued with the `-j2` option. This option defines the number of parallel threads (or `j`obs) that are used to compile the source code. Change `-j2` to `-j<number_of_cpu_cores_in_your_system>` to speed up the compilation of the libraries.

```sh
(cd eProsima_FastCDR-1.0.8-Linux && ./configure --libdir=/usr/lib && make -j2 && sudo make install)
(cd eProsima_FastRTPS-1.7.1-Linux && ./configure --libdir=/usr/lib && make -j2 && sudo make install)
rm -rf requiredcomponents eprosima_fastrtps-1-7-1-linux.tar.gz
```

> **Note** More "generic" instructions, which additionally cover installation from source, can be found here: [Fast RTPS installation](../setup/fast-rtps-installation.md).

## Dependencias de simulación

The dependencies for the Gazebo and jMAVSim simulators listed below. You should minimally install jMAVSim to make it easy to test the installation. Additional information about these and other supported simulators is covered in: [Simulation](../simulation/README.md).

### jMAVSim

Install the dependencies for [jMAVSim Simulation](../simulation/jmavsim.md).

    # jMAVSim simulator
    sudo apt-get install ant openjdk-8-jdk openjdk-8-jre -y
    

### Gazebo

> **Note** If you're going work with ROS then follow the [ROS/Gazebo](#rosgazebo) instructions in the following section (these install Gazebo automatically, as part of the ROS installation).

Install the dependencies for [Gazebo Simulation](../simulation/gazebo.md).

    # Gazebo simulator
    sudo apt-get install protobuf-compiler libeigen3-dev libopencv-dev -y
    sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
    ## Setup keys
    wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
    ## Update the debian database:
    sudo apt-get update -y
    ## Install Gazebo9
    sudo apt-get install gazebo9 -y
    ## For developers (who work on top of Gazebo) one extra package
    sudo apt-get install libgazebo9-dev -y
    

> **Tip** PX4 works with Gazebo 7, 8, and 9. The [installation instructions](http://gazebosim.org/tutorials?tut=install_ubuntu&cat=install) above are for installing Gazebo 9.

### ROS/Gazebo

Install the dependencies for [ROS/Gazebo](../ros/README.md) ("Melodic"). These include Gazebo9 (the default version that comes with ROS Melodic). The instructions come from the ROS Wiki [Ubuntu page](http://wiki.ros.org/kinetic/Installation/Ubuntu).

> **Note** ROS Melodic requires Ubuntu 18.04 (and later). It cannot be installed on Ubuntu 16.04.

```sh
# ROS Melodic/Gazebo
## Gazebo dependencies
sudo apt-get install protobuf-compiler libeigen3-dev libopencv-dev -y

## ROS Gazebo: http://wiki.ros.org/melodic/Installation/Ubuntu
## Setup keys
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
## For keyserver connection problems substitute hkp://pgp.mit.edu:80 or hkp://keyserver.ubuntu.com:80 above.
sudo apt-get update
## Get ROS/Gazebo
sudo apt install ros-melodic-desktop-full -y
## Initialize rosdep
sudo rosdep init
rosdep update
## Setup environment variables
rossource="source /opt/ros/melodic/setup.bash"
if grep -Fxq "$rossource" ~/.bashrc; then echo ROS setup.bash already in .bashrc;
else echo "$rossource" >> ~/.bashrc; fi
eval $rossource
## Install rosinstall and other dependencies
sudo apt install python-rosinstall build-essential -y
```

Install the [MAVROS \(MAVLink on ROS\)](../ros/mavros_installation.md) package. This enables MAVLink communication between computers running ROS, MAVLink enabled autopilots, and MAVLink enabled GCS.

> **Tip** MAVROS can be installed as an Ubuntu package or from source. Source is recommended for developers.

```sh
## Create catkin workspace (ROS build system)
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws

## Install dependencies
sudo apt-get install python-wstool python-rosinstall-generator python-catkin-tools -y

## Initialise wstool
wstool init ~/catkin_ws/src

## Build MAVROS
### Get source (upstream - released)
rosinstall_generator --upstream mavros | tee /tmp/mavros.rosinstall
### Get latest released mavlink package
rosinstall_generator mavlink | tee -a /tmp/mavros.rosinstall
### Setup workspace & install deps
wstool merge -t src /tmp/mavros.rosinstall
wstool update -t src
rosdep install --from-paths src --ignore-src --rosdistro melodic -y
```

> **Note** If you use a Ubuntu-based distro and the command `rosdep install --from-paths src --ignore-src --rosdistro melodic -y` fails, you can try to force the command to run by executing `rosdep install --from-paths src --ignore-src --rosdistro melodic -y --os ubuntu:bionic`

```sh
## Build!
catkin build
## Re-source environment to reflect new packages/build environment
catkin_ws_source="source ~/catkin_ws/devel/setup.bash"
if grep -Fxq "$catkin_ws_source" ~/.bashrc; then echo ROS catkin_ws setup.bash already in .bashrc;
else echo "$catkin_ws_source" >> ~/.bashrc; fi
source ~/.bashrc
```

## Hardware basado en NuttX

Install the following dependencies to build for NuttX based hardware: Pixhawk, Pixfalcon, Pixracer, Pixhawk 3, Intel® Aero Ready to Fly Drone.

> **Note** Packages with specified versions should be installed with the specified package version.

```sh
sudo apt-get install python-serial openocd \
    flex bison libncurses5-dev autoconf texinfo \
    libftdi-dev libtool zlib1g-dev -y
```

Remove any old versions of the arm-none-eabi toolchain.

```sh
sudo apt-get remove gcc-arm-none-eabi gdb-arm-none-eabi binutils-arm-none-eabi gcc-arm-embedded
sudo add-apt-repository --remove ppa:team-gcc-arm-embedded/ppa
```

<!-- import GCC toolchain common documentation --> {% include "_gcc_toolchain_installation.md" %}

## Snapdragon Flight

Setup instructions for Snapdragon Flight are provided in the *PX4 User Guide*:

* [Entorno de desarrollo](https://docs.px4.io/en/flight_controller/snapdragon_flight_dev_environment_installation.html)
* [Instalación del software](https://docs.px4.io/en/flight_controller/snapdragon_flight_software_installation.html)
* [Configuración](https://docs.px4.io/en/flight_controller/snapdragon_flight_configuration.html)

## Raspberry Pi Hardware

Developers working on Raspberry Pi hardware need to download a ARMv7 cross-compiler, either GCC or clang. The current recommended toolchain for raspbian can be cloned from `https://github.com/raspberrypi/tools.git` (at time of writing 4.9.3). The `PATH` environmental variable should include the path to the gcc cross-compiler collection of tools (e.g. gcc, g++, strip) prefixed with `arm-linux-gnueabihf-`.

```sh
git clone https://github.com/raspberrypi/tools.git ${HOME}/rpi-tools

# test compiler
$HOME/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-gcc -v

# permanently update PATH variable by modifying ~/.profile
echo 'export PATH=$PATH:$HOME/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin' >> ~/.profile

# update PATH variable only for this session
export PATH=$PATH:$HOME/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
```

### clang

In order to use clang, you also need GCC.

Download clang for your specific distribution from [LLVM Download page](http://releases.llvm.org/download.html) and unpack it. Assuming that you've unpacked clang to `CLANG_DIR`, and `clang` binary is available in `CLANG_DIR/bin`, and you have the GCC cross-compiler in `GCC_DIR`, you will need to setup the symlinks for clang in the `GCC_DIR` bin dir, and add `GCC_DIR/bin` to `PATH`.

Example below for building PX4 firmware out of tree, using CMake.

```sh
ln -s <CLANG_DIR>/bin/clang <GCC_DIR>/bin/clang
ln -s <CLANG_DIR>/bin/clang++ <GCC_DIR>/bin/clang++
export PATH=<GCC_DIR>/bin:$PATH

cd <PATH-TO-PX4-SRC>
mkdir build/posix_rpi_cross_clang
cd build/posix_rpi_cross_clang
cmake \
-G"Unix Makefiles" \
-DCONFIG=posix_rpi_cross \
-DCMAKE_C_COMPILER=clang \
-DCMAKE_CXX_COMPILER=clang++ \
..
```

### Compilación Nativa

Additional developer information for using PX4 on Raspberry Pi (including building PX4 natively) can be found here: [Raspberry Pi 2/3 Navio2 Autopilot](https://docs.px4.io/en/flight_controller/raspberry_pi_navio2.html).

## Parrot Bebop

Developers working with the Parrot Bebop should install the RPi Linux Toolchain. Follow the description under [Raspberry Pi hardware](#raspberry-pi-hardware).

Next, install ADB.

```sh
sudo apt-get install android-tools-adb -y
```

## Herramientas adicionales

After setting up the build/simulation toolchain, see [Additional Tools](../setup/generic_dev_tools.md) for information about other useful tools.

## Siguientes Pasos

Once you have finished setting up the environment, continue to the [build instructions](../setup/building_px4.md).