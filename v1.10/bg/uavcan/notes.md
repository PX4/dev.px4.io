# Various Notes

This is a collection of tips and tricks to solve issues when setting up or working with the UAVCAN.

### Arm but motors not spinning

If the PX4 Firmware arms but the motors do not start to rotate, check the parameter **UAVCAN\_ENABLE**. It should be set to 3 in order to use the ESCs connected via UAVCAN as output. Moreover, if the motors do not start spinning before thrust is increased, check **UAVCAN\_ESC\_IDLT** and set it to one.

### Debugging with Zubax Babel

A great tool to debug the transmission on the UAVCAN bus is the [Zubax Babel](https://docs.zubax.com/zubax_babel) in combination with the [GUI tool](http://uavcan.org/GUI_Tool/Overview/). They can also be used independently from Pixhawk hardware in order to test a node or manually control UAVCAN enabled ESCs.