# Windows Installation Instructions

To develop for PX4 on Windows, follow the instructions in [Windows Cygwin Toolchain](../setup/dev_env_windows_cygwin.md).

> **Tip** The *Cygwin toolchain* supports building for NuttX/Pixhawk and jMAVSim simulator targets. If you want to build for [other targets](/setup/dev_env.md#supported-targets), consider setting up a dual boot system with [Ubuntu Linux](http://ubuntu.com).

## Additional Tools

After setting up the build/simulation toolchain, see [Additional Tools](../setup/generic_dev_tools.md) for information about other useful "general development" tools.

## Next Steps

Once you have finished setting up the environment, continue to the [build instructions](../setup/building_px4.md).

## Other Windows Toolchains

There are a number of other legacy/alternative solutions that may be of interest to some developers. A comparison of the options is provided below.

> **Note** The [Cygwin Toolchain](../setup/dev_env_windows_cygwin.md) is the only one that is supported by the PX4 dev team. It is regularly tested as part of our continuous integration system and is known to be better performing than the other alternatives.

|                         | [Cygwin Toolchain](../setup/dev_env_windows_cygwin.md) **(Supported)** | [Virtual Machine Toolchain](../setup/dev_env_windows_vm.md) | [Bash on Windows Toolchain](../setup/dev_env_windows_bash_on_win.md) | [Msys Toolchain](../setup/dev_env_windows_msys.md) |
| ----------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------------------------- | -------------------------------------------------- |
| Installation            | MSI installer or Script                                                | Manual (Hard)                                               | Script                                                               | NSIS Installer                                     |
| Native binary execution | yes                                                                    | no                                                          | no                                                                   | yes                                                |
| Performance             | ++                                                                     | --                                                          | -                                                                    | ++                                                 |
| ARM Targets             | ++ (quick)                                                             | + (VM USB)                                                  | +                                                                    | - (broken)                                         |
| Simulation jMAVSim      | ++                                                                     | +                                                           | +                                                                    | --                                                 |
| Simulation gazebo       | - (not yet)                                                            | + (slow)                                                    | + (slow)                                                             | --                                                 |
| Support                 | +                                                                      | ++ (Linux)                                                  | +/-                                                                  | --                                                 |
| Comments                |                                                                        |                                                             |                                                                      |                                                    |

- New in 2018
- Slim setup
- Portable

|

- Full Linux features
- CPU & RAM intensive
- Disk space intensive

|

- Simulation UI is a "hack".
- Windows 10 only
- Essentially a VM

|

- No support
- No documentation
- No simulation

|