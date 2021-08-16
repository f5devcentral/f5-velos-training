======================
Initial Setup of VELOS
======================

System Controller Setup
=======================

Connect a console or terminal server to each of the system controllers console ports. Login as admin/admin (you’ll be prompted to change the password) and access the F5OS CLI. F5OS utilizes ConfD for configuration management of F5OS and will be a familiar navigation experience if you have used it on other products. The CLI supports command completion and online help and is easy to navigate. There are show commands to display current configurations and status, and a **config** mode to alter current configuration.

Once logged in you can display the current running configuration by issuing the command **show running-config**.


syscon-2-active# show running-config
image controller config os os 1.2.0-0333
!
image controller config services service 1.2.0-0333
!
image controller config iso iso 1.2.0-0333
    service 1.2.0-0333
    os      1.2.0-0333
!
image partition config os os 1.2.0-0333
!
image partition config services service 1.2.0-0333
!
image partition config iso iso 1.2.0-0333
    service 1.2.0-0333
    os      1.2.0-0333
!
partitions partition bigpart
config enabled
…

Configuration is automatically synchronized between the primary and standby system controllers. The configuration of both controllers can be defined from the primary system controller, there is no need to login to the secondary system controller to configure an IP address. You’ll know which system controller is primary by the prompt:

An example of the Active/Primary system controller:

syscon-2-active#

An example of the Secondary/Standby system controller:

syscon-1-standby#

If you are logged into the standby system controller then you will not be able to enter config mode. You must be on the primary/active system controller.

syscon-1-standby# config 
Aborted: node is in readonly mode

Interface Configuration 

The out-of-band Ethernet interfaces on each system controller can be configured to run independently, or they may be put into a common Link Aggregation Group to provide added redundancy. To alter any configuration, you must enter config mode:

syscon-2-active# config
Entering configuration mode terminal
syscon-2-active(config)#

Internal Chassis IP Ranges

The VELOS systems ship with a default internal RFC6598 address space of 100.64.0.0/12. This should be sufficient for most production environments. You can verify this with the following command.

syscon-2-active# show system network 
system network state configured-network-range-type RFC6598
system network state configured-network-range 100.64.0.0/12
system network state configured-chassis-id 1
system network state active-network-range-type RFC6598
system network state active-network-range 100.64.0.0/12
system network state active-chassis-id 1
syscon-2-active# 

This address range never leaves the inside of the chassis and will not interfere with any communication outside the VELOS chassis. There can however be address collisions if a device trying to manage VELOS via the out-of-band management port falls within this range, or an external management device or service falls within this range and communicated with VELOS over its out-of-band networking. This may result in VELOS not able to communicate with those devices.

Some examples would be any client trying to access the F5OS or tenant out-of-band interfaces to reach its’ CLI, GUI, or API. Other examples would be external services such as SNMP, DNS, NTP, SNMP, Authentication that have addresses that fall within the RFC6598 address space. You may experience connectivity problems with these types of clients/services if there is an overlap. Note that this does not affect the data plane / in-band interfaces, it only affects communication to the out-of-band interfaces. 

If there is the potential for conflict with external devices that fall within this range that need to communicate with VELOS, then there are options to change the configured-network-range-type to one of sixteen different blocks within the RFC1918 address space. Changing this will require a complete chassis power-cycle, rebooting is not sufficient.  Please consult with F5 prior to making any changes to the internal addresses.


syscon-2-active(config)# system network config network-range-type RFC <Hit Tab>
Possible completions:
  RFC1918   VELOS system uses 10.[0-15]/12 as specified by RFC1918
  RFC6598   VELOS system uses 100.64/10 as specified by RFC6598
syscon-2-active(config)# system network config network-range-type RFC1918

If changing to one of the RFC1918 address spaces, you will need to choose from one of 16 prefix ranges as seen below. You should ensure that this will not overlap with a customer’s current address space:

syscon-2-active(config)# system network config network-range-type RFC1918 prefix ?
Description: 
The network prefix index is used to select the range of IP addresses
used internally within the chassis.  The network prefix should be
selected such that internal chassis addresses do not overlap with
site-local addresses that are accessible to the chassis.

Network Prefix Index       Chassis Network Range
0                          10.[0-15].0.0/12
1                          10.[16-31].0.0/12
2                          10.[32-47].0.0/12
3                          10.[48-63].0.0/12
4                          10.[64-79].0.0/12
5                          10.[80-95].0.0/12
6                          10.[96-111].0.0/12
7                          10.[112-127].0.0/12
8                          10.[128-143].0.0/12
9                          10.[144-159].0.0/12
10                         10.[160-175].0.0/12
11                         10.[176-191].0.0/12
12                         10.[192-207].0.0/12
13                         10.[208-223].0.0/12
14                         10.[224-239].0.0/12
15                         10.[240-255].0.0/12
Possible completions:
  <unsignedByte, 0 .. 15>[0]
syscon-2-active(config)# system network config network-range-type RFC1918 prefix 15
	syscon-2-active(config)# commit
Commit complete.

Note: This change will not take effect until the chassis is power cycled. A complete power cycle is required in order to convert existing internal address space to the new address space, a reboot of individual chassis components is not sufficient.
