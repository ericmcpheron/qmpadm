# QMPADM

Manage running KVM zones using the Qemu Machine Protocol (QMP).
`qmpadm` connects to a KVM zone's vm.qmp socket located under `/zones/<zone uuid>/root/tmp/vm.qmp`
 and passes message to and from the socket.

# Installation

Download `qmpadm` into the SmartOS Global Zone.

Example install into `/opt/local/sbin`

1. `mkdir -p /opt/local/sbin` (
2. `cd /opt/local/sbin`
3. `curl -kO {url}`
4. `chmod 755 qmpadm`

## Dependencies

There are two dependencies which should be included with the normal SmartOS install. This is 
documented here should these files change with a later release.

- `/usr/node/node_modules/onlyif` - Verifies command is run in the global zone by a root user.
- `/usr/vm/node_modules/qmp` - Required to pass messages to the QMP socket.

# Usage

- `qmpadm [options] <zone uuid> <command> [<args>...]`
- `qmpadm help <command>`

## Options

| Option                      | Description                                  |
|-----------------------------|----------------------------------------------|
|`-h`,`--help`                | Show this help message and exit              |
|`--version`                  | Show version and exit                        |
| `-v`,`--verbose`            | Enable verbose output including QMP messages |
|`-p <pool>`,`--pool  <pool>` | Set pool containing the KVM zone to manage   |

## Examples

- Display all available commands

  `qmpadm help`

- Show help for a specific command

  `qmpadm help <command>`

- Show block devices

  `qmpadm <zone uuid> query-block`

- Change removable media or VNC configuration
  - `-d` is the device name see `query-block`.
  - `-t` is the target file or item. File paths are relative to the zone path.
  - `-a` additional arguments (optional)

 `qmpadm <zone uuid> change -d ide1-cd0 -t /virtio.iso`

- Eject cd-rom iso.
  - `-d` is the device name see `query-block`.
  - `-f` force eject

  `qmpadm <zone uuid> eject -d ide1-cd0`
  `qmpadm <zone uuid> eject -d ide1-cd0 -f`

- Reset the emulator

  `qmpadm <zone uuid> system_reset`

- Power off the emulator

  `qmpadm <zone uuid> system_powerdown`
  
# Not Functioning or Unsupported Commands

- `balloon`: Memory ballooning not supported in the SmartOS version of KVM.

- `migrate`: Migrate is able to save the VM state and transfer it to the destination, but fails to 
  load on the destination server. The error message I receive is `qemu: warning: error while 
  loading state for instance 0x0 of device 'kvmclock'`.
