==========================================
Initial Setup Within the Chassis Partition
==========================================

Chassis partitions are completely separate management entities that are managed outside of the system controllers but are still considered part of the F5OS platform layer. If you have properly setup a chassis partition and assigned an out-of-band management IP address, you will be able to access it via its own F5OS-C CLI, webUI, and API. The chassis partitions only have a single out-of-band IP address, and the system is resilient in that the single IP address should be reachable as long as one blade in the partition is active. There is no way to access the chassis partition via in-band networks, as the chassis partition does not have an option for in-band IP interfaces. At first login you will be prompted to change the default chassis partition password.

.. image:: images/initial_setup_of_velos_chassis_partitions/image1.png
  :align: center
  :scale: 70% 

---------------------------
Chassis Partition Dashboard
---------------------------

The chassis partition **Dashboard** will provide a visual system summary of the partition, and which slots are assigned to it. It will also list the total number of vCPUs available for multitenancy and how many are currently in use.  There is also a tenant overview showing a quick summary of tenant status and basic parameters. Lastly it will display **System Summary** stats under the tab of that name.

.. image:: images/initial_setup_of_velos_chassis_partitions/image2.png
  :align: center
  :scale: 70% 

The **Tenant** tab will display all currently running tenants on the chassis partition you are connected to.

.. image:: images/initial_setup_of_velos_chassis_partitions/tenant-dashboard.png
  :align: center
  :scale: 70% 

If you click on the **Network** tab, then each front panel port will be displayed, and it will be color coded red or green based on its current status.

.. image:: images/initial_setup_of_velos_chassis_partitions/image3.png
  :align: center
  :scale: 70%

If you click on the **CPU** tab, then CPU utilization across different time periods will be displayed for each of the blades and for F5OS and tenants within this chassis partition. You can also see how the vCPUs in this chassis partition are assigned.

.. image:: images/initial_setup_of_velos_chassis_partitions/cpu-dashboard.png
  :align: center
  :scale: 70%     

If there are any active alarms, they will be displayed on the **Active Alarms** page.

.. image:: images/initial_setup_of_velos_chassis_partitions/image5.png
  :align: center
  :scale: 70%   

----------------------------
Chassis Partition Networking
----------------------------

Before configuring any tenant, you’ll need to setup networking for the chassis partition. All in-band networking is configured within the chassis partition layer, and all chassis partitions are completely isolated from each other. You cannot share any in-band networking internally between different chassis partitions.

--------------------------------
Network Settings - > Port Groups
--------------------------------

The portgroup component is used to control the mode of the physical port. This controls whether the port is bundled or unbundled, and the port speed. The term portgroup is used rather than simply “port” because some front panel ports may accept different types of optics. Depending on the portgroup mode value, a different FPGA version is loaded, and the speed of the port is adjusted accordingly. The user can modify the portgroup mode as needed through the F5OS CLI, webUI, or API.


.. image:: images/velos_networking/image9.png
  :width: 45%

.. image:: images/velos_networking/image10.png
  :width: 45%

In releases prior to F5OS-C 1.5.1 both ports on a BX110 blade must be configured for the same mode, both ports must be either 100GB, 40GB, 4 x 25GB, or 4 x 10GB; there was no support for mixing modes on the same blade. You could have different options across different blades within the same chassis partition, but within a single blade, the ports had to be the same. F5OS-C 1.5.1 introduced more flexible options for port group configurations within the same blade. The table below shows the new heterogeneous port modes that were introduced in F5OS-C 1.5.1.

.. image:: images/velos_networking/image10a.png
   :align: center
   :scale: 70%



Below is an example of the chassis partition webUI Port Groups screen with BX110 blades. Note that any changes in configuration will require a reboot of the blade to load a new FPGA bitstream image.

.. image:: images/velos_networking/image11.png
   :align: center
   :scale: 70%

For the BX520 blade there are two physical ports (1.0 & 2.0). Port 1.0 is a QSFP-DD port, that supports either 100Gb optics or 4 x 100Gb (targeted to be generally available  F5OS-C 1.8.1 release) connections with the proper optic and breakout cable. For the current release, 100Gb connectivity (SR-4 & LR-4) is supported, 4 x 100Gb support is targeted to be generally available in a the F5OS-C 1.8.1 release. The second port (2.0) is also a QSFP-DD port; however it supports 400Gb optics today (FR-4), and the option of 4 x 100Gb with the proper optic and breakout cables is targeted to be generally available in the F5OS-C 1.8.1 release. 

.. image:: images/velos_networking/image11a.png
  :width: 45%

.. image:: images/velos_networking/image11b.png
  :width: 35%

.. image:: images/velos_networking/image11c.png
  :align: center
  :width: 55%

Below is an example of the chassis partition webUI Port Groups screen with BX520 blades. Note that any changes in configuration will require a reboot of the blade to load a new FPGA bitstream image. Port1 is currently hardcoded for 100Gb, and port2 is currently hard coded at 400gb. Breakout support for 4 x 100Gb is targeted to be generally available in F5OS-C 1.8.1.

.. image:: images/velos_networking/image11d.png
   :align: center
   :scale: 70%

Configuring PortGroups from the webUI
-----------------------------------

To configure Portgroups go to **Network Settings > Port Groups** in the chassis partition webUI. This should be configured before any Interface, VLAN, or LAG configuration as changing the portgroup mode will alter interface numbering on the blade. Note the warning at the top of the webUI page:

.. image:: images/initial_setup_of_velos_chassis_partitions/image8.png
  :align: center
  :scale: 70% 

If you make a change the blade will be forced to reboot to load a new bitstream image into the FPGA.

Configuring PortGroups from the CLI
-----------------------------------

Portgroups can be configured from the chassis partition CLI using the **portgroups** command in **config** mode. The following command will set interface 1/1 for 100GB:

.. code-block:: bash

  Production-1# config
  Entering configuration mode terminal
  Production-1(config)# portgroups portgroup 1/1 config mode MODE_100GB

You must commit for any changes to take affect:

.. code-block:: bash

  Production-1(config)# commit


Possible options for mode on the BX110 blade are MODE_4x10GB,  MODE_4x25GB,  MODE_40GB,  MODE_100GB. For the BX520 blade supported modes for port1 are MODE_100GB and MODE_4x100Gb and for port2: MODE_4x100GB and MODE_400Gb. You can optionally configure the portgroup name and ddm poll frequency. You can display the current configuration of the existing portgroups by running the CLI command **show running-config portgroups**:

.. code-block:: bash

  Production-1# show running-config portgroups 
  portgroups portgroup 1/1
  config name 1/1
  config mode MODE_100GB
  config ddm ddm-poll-frequency 30
  !
  portgroups portgroup 1/2
  config name 1/2
  config mode MODE_100GB
  config ddm ddm-poll-frequency 30
  !
  portgroups portgroup 2/1
  config name 2/1
  config mode MODE_100GB
  config ddm ddm-poll-frequency 30
  !
  portgroups portgroup 2/2
  config name 2/2
  config mode MODE_100GB
  config ddm ddm-poll-frequency 30
  !
  Production-1# 

Configuring PortGroups from the API
-----------------------------------

To list the current portgroup configuration issue the following API call:

.. code-block:: bash

  GET https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/f5-portgroup:portgroups

The output of the API call will be similar to the example below.

.. code-block:: json

  {
      "f5-portgroup:portgroups": {
          "portgroup": [
              {
                  "portgroup_name": "1/1",
                  "config": {
                      "name": "1/1",
                      "mode": "MODE_100GB",
                      "f5-ddm:ddm": {
                          "ddm-poll-frequency": 30
                      }
                  },
                  "state": {
                      "vendor-name": "F5 NETWORKS INC.",
                      "vendor-oui": "009065",
                      "vendor-partnum": "OPT-0031        ",
                      "vendor-revision": "A0",
                      "vendor-serialnum": "X3CAU1J         ",
                      "transmitter-technology": "850 nm VCSEL",
                      "media": "100GBASE-SR4",
                      "optic-state": "QUALIFIED",
                      "f5-ddm:ddm": {
                          "rx-pwr": {
                              "low-threshold": {
                                  "alarm": "-14.0",
                                  "warn": "-11.0"
                              },
                              "instant": {
                                  "val-lane1": "-0.08",
                                  "val-lane2": "-0.61",
                                  "val-lane3": "-0.19",
                                  "val-lane4": "-0.73"
                              },
                              "high-threshold": {
                                  "alarm": "3.4",
                                  "warn": "2.4"
                              }
                          },
                          "tx-pwr": {
                              "low-threshold": {
                                  "alarm": "-10.0",
                                  "warn": "-8.0"
                              },
                              "instant": {
                                  "val-lane1": "-0.77",
                                  "val-lane2": "-1.01",
                                  "val-lane3": "-1.01",
                                  "val-lane4": "-0.82"
                              },
                              "high-threshold": {
                                  "alarm": "5.0",
                                  "warn": "3.0"
                              }
                          },
                          "temp": {
                              "low-threshold": {
                                  "alarm": "-5.0",
                                  "warn": "0.0"
                              },
                              "instant": {
                                  "val": "23.4609"
                              },
                              "high-threshold": {
                                  "alarm": "75.0",
                                  "warn": "70.0"
                              }
                          },
                          "bias": {
                              "low-threshold": {
                                  "alarm": "0.003",
                                  "warn": "0.005"
                              },
                              "instant": {
                                  "val-lane1": "0.007526",
                                  "val-lane2": "0.007484",
                                  "val-lane3": "0.00752",
                                  "val-lane4": "0.006914"
                              },
                              "high-threshold": {
                                  "alarm": "0.013",
                                  "warn": "0.011"
                              }
                          },
                          "vcc": {
                              "low-threshold": {
                                  "alarm": "2.97",
                                  "warn": "3.135"
                              },
                              "instant": {
                                  "val": "3.2555"
                              },
                              "high-threshold": {
                                  "alarm": "3.63",
                                  "warn": "3.465"
                              }
                          }
                      }
                  }
              },
              {
                  "portgroup_name": "1/2",
                  "config": {
                      "name": "1/2",
                      "mode": "MODE_100GB",
                      "f5-ddm:ddm": {
                          "ddm-poll-frequency": 30
                      }
                  },
                  "state": {
                      "vendor-name": "F5 NETWORKS INC.",
   ....

------------------------------
Network Settings -> Interfaces
------------------------------

Interface numbering will vary depending on the current portgroup configuration. Interfaces will always be numbered by **<blade#>/<port#>**. The number of ports on a blade will change depending on if the portgroup is configured as bundled, or unbundled. If the ports are bundled then ports will be **1/1.0** and **1/2.0** for slot 1, and **2/1.0** and **2/2.0** for slot 2. 

If ports are unbundled, then the port numbering will be **1/1.1, 1/1.2, 1/1.3, and 1/1.4** for the first physical port and **1/2.1, 1/2.2, 1/2.3, and 1/2.4** for the second physical port. Breakout cables will be needed to support the unbundled 25Gb, or 10Gb configurations. Even when multiple chassis partitions are used, the port numbering will stay consistent starting with the blade number. Below is an example of BX110 blade port numbering when all interfaces are bundled. 

.. image:: images/velos_networking/image12.png
  :align: center

Below is an example of BX110 port numbering when all interfaces are unbundled.

.. image:: images/velos_networking/image12a.png
  :align: center


For the BX520 blades interface numbering is different because the BX520 blade takes up two slots. The slot reference for the blade will always be the first of the two slots where the blade is inserted, which will always be an odd number. If the ports are bundled, then ports will be **1/1.0** and **1/2.0** for a blade that occupies slot 1 and slot 2, and **3/1.0** and **3/2.0** for a blade that occupies slots 3 and 4. Below is an example of four BX520 blades within the CX410 chassis and their interface numbering when all ports are bundled.

.. image:: images/velos_networking/bx520-interfaces.png
  :align: center
  :scale: 70%

Below is an example of BX520 port numbering when all interfaces are unbundled into 4 x 100gb interfaces.

.. image:: images/velos_networking/bx520-interfaces-unbundled.png
  :align: center
  :scale: 70%


Configuring Interfaces from the webUI
-----------------------------------

Within the chassis partition webUI the physical ports of all blades within that partition will be visible by going to **Network Settings > Interfaces** page. If there are other chassis partitions in the VELOS system, then those ports will only be seen within their own chassis partition. In the example below this VELOS system has 3 BX110 blades installed, but only two are part of this chassis partition, so you will not see ports from the 3rd blade unless you connect directly to the other chassis partition.

.. image:: images/initial_setup_of_velos_chassis_partitions/image11.png
  :align: center
  :scale: 70%  

You can click on any interface to view its settings or edit them. You can currently change the interface State via the webUI or the **Native VLAN** (untagged) and **Trunk VLANs** (tagged) as long as the interface is not part of a LAG. If the interface is part of the LAG, then the VLAN configuration is done within the LAG rather than the interface.

.. image:: images/initial_setup_of_velos_chassis_partitions/image12.png
  :align: center
  :scale: 70% 

Configuring Interfaces from the CLI
-----------------------------------

Interfaces can be configured in the chassis partition CLI. As mentioned previously, portgroups should be configured for their desired state before configuring any interfaces as the interface numbering may change. In the CLI, enter config mode and then specify the interface you want to configure. If the interface is going to be part of a LAG, then most of the configuration is done within the LAG. Use the command **show running-config interfaces** to see the current configuration:


.. code-block:: bash

  Production-1# show running-config interfaces 
  interfaces interface 1/1.0
  config name 1/1.0
  config type ethernetCsmacd
  config enabled
  config tpid TPID_0X8100
  ethernet config aggregate-id ha
  !
  interfaces interface 1/2.0
  config name 1/2.0
  config type ethernetCsmacd
  config enabled
  config tpid TPID_0X8100
  ethernet config aggregate-id Arista
  !
  interfaces interface 2/1.0
  config name 2/1.0
  config type ethernetCsmacd
  config enabled
  config tpid TPID_0X8100
  ethernet config aggregate-id Arista
  !
  interfaces interface 2/2.0
  config name 2/2.0
  config type ethernetCsmacd
  config enabled
  config tpid TPID_0X8100
  ethernet config aggregate-id ha
  !
  interfaces interface Arista
  config name Arista
  config type ieee8023adLag
  config tpid TPID_0X8100
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 444 555 ]
  !
  interfaces interface ha
  config name ha
  config type ieee8023adLag
  config tpid TPID_0X8100
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 500 ]
  !

To make any changes you will need to enter **config** mode and then enter the **interface** to make changes. Be sure to **commit** any changes as they don’t take effect until the commit is issued.

.. code-block:: bash

  Production-1# config
  Entering configuration mode terminal
  Production-1(config)# interfaces interface 1/1.0
  Production-1(config-interface-1/1.0)# ethernet switched-vlan config trunk-vlans 500
  Production-1(config-interface-1/1.0)# commit

Configuring Interfaces from the API
-----------------------------------

The following API command will list all the current interfaces within the current chassis partition with their current configuration and status: 

.. code-block:: bash

  GET https://{{velos_chassis1_chassis_partition2_ip}}:8888/restconf/data/openconfig-interfaces:interfaces

The output of the API call will be similar to the example below.

.. code-block:: json

    {
      "openconfig-interfaces:interfaces": {
          "interface": [
              {
                  "name": "3/1.0",
                  "config": {
                      "name": "3/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true,
                      "openconfig-vlan:tpid": "openconfig-vlan-types:TPID_0X8100"
                  },
                  "state": {
                      "name": "3/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "0",
                          "in-unicast-pkts": "0",
                          "in-broadcast-pkts": "0",
                          "in-multicast-pkts": "0",
                          "in-discards": "0",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "2820",
                          "out-unicast-pkts": "0",
                          "out-broadcast-pkts": "0",
                          "out-multicast-pkts": "30",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_DEFAULTED"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d1:00",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      },
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  500
                              ]
                          }
                      }
                  }
              },
              {
                  "name": "3/2.0",
                  "config": {
                      "name": "3/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true,
                      "openconfig-vlan:tpid": "openconfig-vlan-types:TPID_0X8100"
                  },
                  "state": {
                      "name": "3/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "62245397142",
                          "in-unicast-pkts": "152194827",
                          "in-broadcast-pkts": "62238",
                          "in-multicast-pkts": "297616",
                          "in-discards": "18882",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "61962689001",
                          "out-unicast-pkts": "167540438",
                          "out-broadcast-pkts": "855",
                          "out-multicast-pkts": "60",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_DEFAULTED"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d1:01",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      },
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  444,
                                  555
                              ]
                          }
                      }
                  }
              }
          ]
      }
  }


To configure interfaces (that are not part of a LAG), use the following **PATCH** API call. In the example below, VLANs are being assigned to the physical interfaces.

.. code-block:: bash

  PATCH https://{{velos_chassis1_chassis_partition2_ip}}:8888/restconf/data/openconfig-interfaces:interfaces

Enter the following into the body of the API request.

.. code-block:: json

  {
      "openconfig-interfaces:interfaces": {
          "interface": [
              {
                  "name": "3/1.0",
                  "openconfig-if-ethernet:ethernet": {
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  500
                              ]
                          }
                      }
                  }
              },
              {
                  "name": "3/2.0",
                  "openconfig-if-ethernet:ethernet": {
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  444,
                                  555
                              ]
                          }
                      }
                  }
              }
          ]
      }
  }

--------------------------
Network Settings -> VLANs
--------------------------

All in-band networking including VLANs are configured in the VELOS chassis partition layer, and just like vCMP guests inherit VLANs, VLANs will be inherited by VELOS tenants. This allows administrators to assign the VLANs that are authorized for use by the tenant at the chassis partition layer, and then within the tenant there is no ability to configure lower-level networking like interfaces, LAG’s and VLANs. 

VELOS supports both tagged (802.1Q) and untagged VLAN interfaces externally. VLANs can be configured from the CLI, webUI, or API.

**Note: 802.1Q-in-Q (double VLAN tagging) is not currently supported on the VELOS platform.**

Configuring VLANs from the webUI
------------------------------

VLANs can be created in the chassis partition webUI under **Network Settings > VLANs**. VLANs are not shared across chassis partitions, and each partition must configure its own set of VLANs. When adding a new VLAN you will define a Name and a VLAN ID. When you assign this VLAN to an interface or LAG you will determine if you want it to be untagged by configuring it as a Native VLAN or tagged by adding it as a Trunked VLAN.

.. image:: images/initial_setup_of_velos_chassis_partitions/image13.png
  :align: center
  :scale: 70%

.. image:: images/initial_setup_of_velos_chassis_partitions/image14.png
  :align: center
  :scale: 70%


Configuring VLANs from the CLI
------------------------------

VLANs can be configured within the chassis partition CLI. Once VLANs are created they can either be assigned to a physical interfaces or LAGs within the chassis partition. VLANs must be given a name and a VLAN ID. You can choose if a VLAN is tagged or untagged within the physical interface or LAG configuration.

To show the current configured VLANs and their options use the command **show running-config vlans**.

.. code-block:: bash

  Production-1# show running-config vlans
  vlans vlan 500
  config name HA-VLAN
  !
  vlans vlan 501
  config name HA-VLAN-Tenant1
  !
  vlans vlan 502
  config name HA-VLAN-Tenant2
  !
  vlans vlan 503
  config name HA-VLAN-Tenant3
  !
  vlans vlan 3010
  config name Internal-VLAN
  !
  vlans vlan 3011
  config name External-VLAN
  !


You can also see configured state of VLANs by running the **show vlans** command:

.. code-block:: bash

  Production-1# show vlans
  VLAN                   
  ID    INTERFACE        
  -----------------------
  500   HA-Interconnect  
  501   HA-Interconnect  
  502   HA-Interconnect  
  503   HA-Interconnect  
  3010  Arista           
  3011  Arista  

There are a few other VLAN related commands to show the configuration and running state of **vlan-listeners**. The command **show running-config vlan-listeners** will show the current configuration. A VLAN listener is created for each VLAN and is responsible for rebroadcasting traffic within the VLAN.

**NOTE: For Shared VLANs amongst different tenants, the VLAN must be tied to an external interface or LAG in order for the VLAN listener to be created.** 

.. code-block:: bash

  Production-1# show running-config vlan-listeners 
  vlan-listeners vlan-listener Arista 444
  config entry-type RBCAST-LISTENER
  config owner rbcast
  config ifh-fields ndi-id 4095
  config ifh-fields svc 5
  config ifh-fields vtc 32
  config ifh-fields sep 15
  config ifh-fields mirroring disabled
  config service-ids [ 8 10 ]
  !
  vlan-listeners vlan-listener Arista 555
  config entry-type RBCAST-LISTENER
  config owner rbcast
  config ifh-fields ndi-id 4095
  config ifh-fields svc 5
  config ifh-fields vtc 32
  config ifh-fields sep 15
  config ifh-fields mirroring disabled
  config service-ids [ 8 10 ]
  !
  vlan-listeners vlan-listener ha 500
  config entry-type RBCAST-LISTENER
  config owner rbcast
  config ifh-fields ndi-id 4095
  config ifh-fields svc 5
  config ifh-fields vtc 32
  config ifh-fields sep 15
  config ifh-fields mirroring disabled
  config service-ids [ 8 10 ]
  !

The **show vlan-listeners** command will show the current state:

.. code-block:: bash

  Production-1# show vlan-listeners 
                                                  NDI                                             SERVICE  
  INTERFACE        VLAN  ENTRY TYPE       OWNER    ID    SVC  VTC  SEP  DMS  DID  CMDS  MIRRORING  IDS      
  ----------------------------------------------------------------------------------------------------------
  Arista           444   RBCAST-LISTENER  rbcast   4095  5    32   15   -    -    -     disabled   [ 8 9 ]  
  Arista           555   RBCAST-LISTENER  rbcast   4095  5    32   15   -    -    -     disabled   [ 8 9 ]  
  HA-Interconnect  500   VLAN-LISTENER    tenant2  4095  9    -    15   -    -    -     disabled   -        
  HA-Interconnect  501   VLAN-LISTENER    tenant1  4095  8    -    15   -    -    -     disabled   -     

Configuring VLANs from the API
------------------------------

To configure VLANs use the following API command and JSON body. This will configure multiple VLANs along with their VLAN IDs. After the VLANs are created you will be able to assign then to either interfaces or LAGs.

.. code-block:: bash

  PATCH https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/

Enter the following in the body of the API request.

.. code-block:: json

    {
        "openconfig-vlan:vlans": {
            "vlan": [
                {
                    "vlan-id": "3010",
                    "config": {
                        "vlan-id": 3010,
                        "name": "Internal-VLAN"
                    }
                },
                {
                    "vlan-id": "3011",
                    "config": {
                        "vlan-id": 3011,
                        "name": "External-VLAN"
                    }
                },
                {
                    "vlan-id": "500",
                    "config": {
                        "vlan-id": 500,
                        "name": "HA-VLAN"
                    }
                },
                {
                    "vlan-id": "501",
                    "config": {
                        "vlan-id": 501,
                        "name": "HA-VLAN-Tenant1"
                    }
                },
                {
                    "vlan-id": "502",
                    "config": {
                        "vlan-id": 502,
                        "name": "HA-VLAN-Tenant2"
                    }
                },
                {
                    "vlan-id": "503",
                    "config": {
                        "vlan-id": 503,
                        "name": "HA-VLAN-Tenant3"
                    }
                }
            ]
        }
    }


The following command will list the configuration and status of all VLANs within the current chassis partition:

.. code-block:: bash

  GET https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/openconfig-vlan:vlans

The response will be similar to the example below.

.. code-block:: json

    {
        "openconfig-vlan:vlans": {
            "vlan": [
                {
                    "vlan-id": 500,
                    "config": {
                        "vlan-id": 500,
                        "name": "HA-VLAN"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "HA-Interconnect"
                                }
                            }
                        ]
                    }
                },
                {
                    "vlan-id": 501,
                    "config": {
                        "vlan-id": 501,
                        "name": "HA-VLAN-Tenant1"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "HA-Interconnect"
                                }
                            }
                        ]
                    }
                },
                {
                    "vlan-id": 502,
                    "config": {
                        "vlan-id": 502,
                        "name": "HA-VLAN-Tenant2"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "HA-Interconnect"
                                }
                            }
                        ]
                    }
                },
                {
                    "vlan-id": 503,
                    "config": {
                        "vlan-id": 503,
                        "name": "HA-VLAN-Tenant3"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "HA-Interconnect"
                                }
                            }
                        ]
                    }
                },
                {
                    "vlan-id": 3010,
                    "config": {
                        "vlan-id": 3010,
                        "name": "Internal-VLAN"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "Arista"
                                }
                            }
                        ]
                    }
                },
                {
                    "vlan-id": 3011,
                    "config": {
                        "vlan-id": 3011,
                        "name": "External-VLAN"
                    },
                    "members": {
                        "member": [
                            {
                                "state": {
                                    "interface": "Arista"
                                }
                            }
                        ]
                    }
                }
            ]
        }
    }

------------------------
Network Settings -> LAGs
------------------------

All in-band networking including LAGs are configured in the VELOS chassis partition layer. The admin will configure interfaces and/or LAGs, and they will assign VLANs to those physical interfaces. Tenants will then inherit the VLANs that are assigned to them when they are created. It is recommended to spread LAG members across blades for added redundancy. 

Configuring LAGs from the webUI
-----------------------------

Link Aggregation Groups (LAGs) can be configured in the chassis partition webUI via the **Network Settings > LAGs** page:

.. image:: images/initial_setup_of_velos_chassis_partitions/image15.png
  :align: center
  :scale: 70% 

You can add a new LAG or edit an existing one. For **LAG Type** the options are **LACP** or **STATIC**. If you choose LACP, then you have additional options for **LACP Interval** (**SLOW** or **FAST**) and **LACP Mode** (**ACTIVE** or **PASSIVE**). LACP best practices should follow previous BIG-IP examples as outlined in the links below. Note in BIG-IP the term **Trunks** is used in place of **LAG** which is used in the F5OS layer in VELOS: 

`K1689: Overview of trunks on BIG-IP platforms <https://my.f5.com/manage/s/article/K1689>`_

`K13142: Configure the BIG-IP system to interface with Cisco virtual PortChannel <https://my.f5.com/manage/s/article/K13142>`_


The following solution article provides guidance for setting up VELOS LAG interfaces and LACP with Cisco Nexus 9000 series switches:


`K33431212: Configure LAGs with LACP between the VELOS system and Cisco Nexus 9000 series switches <https://my.f5.com/manage/s/article/K33431212>`_


Once you have configured the LAG Type and LACP options, you can add any physical interfaces within this chassis partition to be part of a LAG. Note you cannot add physical interfaces that reside in other chassis partitions as they are completely isolated from each other. Finally, you can configure the **Native VLAN** (for untagged VLAN), and what **Trunked VLANs** (tagged) you’d like to add to this LAG interface.

.. image:: images/initial_setup_of_velos_chassis_partitions/image16.png
  :align: center
  :scale: 70% 

Configuring LAGs from the CLI
-----------------------------

Within the webUI LAGs and LACP parameters are configured within the LAG webUI pages. In the CLI they are broken out into sperate areas. First enter **config** mode and then use the following lacp commands to configure the lacp interfaces:

.. code-block:: bash

  Production-1# config
  Entering configuration mode terminal
  Production-1(config)# lacp interfaces interface Arista config name Arista
  Production-1(config-interface-Arista)# config interval FAST 
  Production-1(config-interface-Arista)# config lacp-mode ACTIVE 
  Production-1(config-interface-Arista)# commit 


Next configure the interface aggregation:

.. code-block:: bash

  Production-1(config)# interfaces interface Arista aggregation config distribution-hash src-dst-ipport  
  Production-1(config-interface-Arista)#  aggregation config lag-type LACP
  Production-1(config-interface-Arista)#  aggregation switched-vlan config trunk-vlans [ 3010 3011 ]
  Production-1(config-interface-Arista)#  commit


You can view the current interface aggregation configurations in the CLI by running the command **show running-config interfaces interface aggregation** command. This will show the current aggregation interfaces, lag-type, distribution hash, and VLANs assigned to each lag:

.. code-block:: bash

  Production-1# show running-config interfaces interface aggregation 
  interfaces interface Arista
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 3010 3011 ]
  !
  interfaces interface HA-Interconnect
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 500 501 502 503 ]
  !
  Production-1#

Finally, you must configure interfaces to be part of the LAG. Below are examples of interface 1/1.0 and 2/2.0 being added to the aggregate-id **HA-Interconnect**, and interfaces 1/2.0 and 2/1.0 being added to the aggregate **Arista**.

.. code-block:: bash

  Production-1# show running-config interfaces 
  interfaces interface 1/1.0
  config type ethernetCsmacd
  config enabled
  ethernet config aggregate-id HA-Interconnect
  !
  interfaces interface 1/2.0
  config type ethernetCsmacd
  config enabled
  ethernet config aggregate-id Arista
  !
  interfaces interface 2/1.0
  config type ethernetCsmacd
  config enabled
  ethernet config aggregate-id Arista
  !
  interfaces interface 2/2.0
  config type ethernetCsmacd
  config enabled
  ethernet config aggregate-id HA-Interconnect
  !
  interfaces interface Arista
  config type ieee8023adLag
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 3010 3011 ]
  !
  interfaces interface HA-Interconnect
  config type ieee8023adLag
  aggregation config lag-type LACP
  aggregation config distribution-hash src-dst-ipport
  aggregation switched-vlan config trunk-vlans [ 500 501 502 503 ]
  !


You can also view the current lacp configuration for each LAG by issuing the **show running-config lacp** CLI command. This will show all the LACP parameters such as the system priority, name, interval, and lacp-mode for each LAG. 

.. code-block:: bash

  Production-1# show running-config lacp
  lacp config system-priority 32768
  lacp interfaces interface Arista
  config name Arista
  config interval FAST
  config lacp-mode ACTIVE
  !
  lacp interfaces interface HA-Interconnect
  config name HA-Interconnect
  config interval FAST
  config lacp-mode ACTIVE
  !
  Production-1# 


To see the status of the LACP interfaces run the command **show lacp**. It is best to widen your terminal screen as the output is dynamic and will display better on a wider terminal screen in more of a table format:

.. code-block:: bash

  Production-1# show lacp
  lacp state system-id-mac 00:94:a1:8e:d0:08
                                                                                                                                                                                                                                  PARTNER  LACP    LACP    LACP    LACP    LACP             
                                              LACP                                                                                                                                        OPER                     PARTNER  PORT  PORT     IN      OUT     RX      TX      UNKNOWN  LACP    
  NAME             NAME             INTERVAL  MODE    SYSTEM ID MAC    INTERFACE  INTERFACE  ACTIVITY  TIMEOUT  SYNCHRONIZATION  AGGREGATABLE  COLLECTING  DISTRIBUTING  SYSTEM ID        KEY   PARTNER ID         KEY      NUM   NUM      PKTS    PKTS    ERRORS  ERRORS  ERRORS   ERRORS  
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Arista           Arista           FAST      ACTIVE  0:94:a1:8e:d0:8  1/2.0      -          ACTIVE    SHORT    IN_SYNC          true          true        true          0:94:a1:8e:d0:8  2     98:5d:82:1d:2c:a9  10       4352  125      713887  713949  0       0       0        0       
                                                                      2/1.0      -          ACTIVE    SHORT    IN_SYNC          true          true        true          0:94:a1:8e:d0:8  2     98:5d:82:1d:2c:a9  10       8320  129      713906  713948  0       0       0        0       
  HA-Interconnect  HA-Interconnect  FAST      ACTIVE  0:94:a1:8e:d0:8  1/1.0      -          ACTIVE    SHORT    IN_SYNC          true          true        true          0:94:a1:8e:d0:8  3     0:94:a1:8e:58:28   3        4224  8448     714114  713959  0       0       0        0       
                                                                      2/2.0      -          ACTIVE    SHORT    IN_SYNC          true          true        true          0:94:a1:8e:d0:8  3     0:94:a1:8e:58:28   3        8448  4224     714155  713959  0       0       0        0       

  Production-1# 


If you have shorter width terminal, then the output above may be condensed as seen below:

.. code-block:: bash

  Production-1# show lacp
  lacp state system-id-mac 00:94:a1:8e:d0:08
  lacp interfaces interface Arista
  state name    Arista
  state interval FAST
  state lacp-mode ACTIVE
  state system-id-mac 0:94:a1:8e:d0:8
  members member 1/2.0
    state activity   ACTIVE
    state timeout    SHORT
    state synchronization IN_SYNC
    state aggregatable true
    state collecting true
    state distributing true
    state system-id  0:94:a1:8e:d0:8
    state oper-key   2
    state partner-id 98:5d:82:1d:2c:a9
    state partner-key 10
    state port-num   4352
    state partner-port-num 125
    state counters lacp-in-pkts 714408
    state counters lacp-out-pkts 714471
    state counters lacp-rx-errors 0
    state counters lacp-tx-errors 0
    state counters lacp-unknown-errors 0
    state counters lacp-errors 0
  members member 2/1.0
    state activity   ACTIVE
    state timeout    SHORT
    state synchronization IN_SYNC
    state aggregatable true
    state collecting true
    state distributing true
    state system-id  0:94:a1:8e:d0:8
    state oper-key   2
    state partner-id 98:5d:82:1d:2c:a9
    state partner-key 10
    state port-num   8320
    state partner-port-num 129
    state counters lacp-in-pkts 714428
    state counters lacp-out-pkts 714469
    state counters lacp-rx-errors 0
    state counters lacp-tx-errors 0
    state counters lacp-unknown-errors 0
    state counters lacp-errors 0
  lacp interfaces interface HA-Interconnect
  state name    HA-Interconnect
  state interval FAST
  state lacp-mode ACTIVE
  state system-id-mac 0:94:a1:8e:d0:8
  members member 1/1.0
    state activity   ACTIVE
    state timeout    SHORT
    state synchronization IN_SYNC
    state aggregatable true
    state collecting true
    state distributing true
    state system-id  0:94:a1:8e:d0:8
    state oper-key   3
    state partner-id 0:94:a1:8e:58:28
    state partner-key 3
    state port-num   4224
    state partner-port-num 8448
    state counters lacp-in-pkts 714647
    state counters lacp-out-pkts 714493
    state counters lacp-rx-errors 0
    state counters lacp-tx-errors 0
    state counters lacp-unknown-errors 0
    state counters lacp-errors 0
  members member 2/2.0
    state activity   ACTIVE
    state timeout    SHORT
    state synchronization IN_SYNC
    state aggregatable true
    state collecting true
    state distributing true
    state system-id  0:94:a1:8e:d0:8
    state oper-key   3
    state partner-id 0:94:a1:8e:58:28
    state partner-key 3
    state port-num   8448
    state partner-port-num 4224
    state counters lacp-in-pkts 714689
    state counters lacp-out-pkts 714492
    state counters lacp-rx-errors 0
    state counters lacp-tx-errors 0
    state counters lacp-unknown-errors 0
    state counters lacp-errors 0
  Production-1# 

Configuring LAGs from the API
-----------------------------

To create a LAG and add interfaces & proper LACP configuration will take a few different API calls. First a Link Aggregation Group (LAG) interface must be created. You will define a Name, specify the state, the LAG-type of LACP, and define which VLANs will use this LAG interface. In the Example below two LAG interfaces are being created (Arista & HA-Interconnect):

.. code-block:: bash

  PATCH https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/

Enter the following in the body of the API request.

.. code-block:: json

    {
        "openconfig-interfaces:interfaces": {
            "interface": [
                {
                    "name": "Arista",
                    "config": {
                        "name": "Arista",
                        "type": "iana-if-type:ieee8023adLag",
                        "enabled": true
                    
                    },
                    "openconfig-if-aggregate:aggregation": {
                        "config": {
                            "lag-type": "LACP",
                            "f5-if-aggregate:distribution-hash": "src-dst-ipport"
                        },
                        "openconfig-vlan:switched-vlan": {
                            "config": {
                                "trunk-vlans": [
                                    3010,
                                    3011
                                ]
                            }
                        }
                    }
                },
                {
                    "name": "HA-Interconnect",
                    "config": {
                        "name": "HA-Interconnect",
                        "type": "iana-if-type:ieee8023adLag",
                        "enabled": true
                        
                    },
                    "openconfig-if-aggregate:aggregation": {
                        "config": {
                            "lag-type": "LACP",
                            "f5-if-aggregate:distribution-hash": "src-dst-ipport"
                        },
                        "openconfig-vlan:switched-vlan": {
                            "config": {
                                "trunk-vlans": [
                                    500,
                                    501,
                                    502,
                                    503
                                ]
                            }
                        }
                    }
                }
            ]
        }
    }


The next step is to add physical interfaces into the LAG group. Interfaces will be added to the aggregate-id that was created in the previous step:

.. code-block:: bash

  PATCH https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/

Enter the following in the body of the API request.

.. code-block:: json

    {
        "openconfig-interfaces:interfaces": {
            "interface": [
                {
                    "name": "1/2.0",
                    "config": {
                        "name": "1/2.0"
                    },
                    "openconfig-if-ethernet:ethernet": {
                        "config": {
                            "openconfig-if-aggregate:aggregate-id": "Arista"
                        }
                    }
                },
                {
                    "name": "2/1.0",
                    "config": {
                        "name": "2/1.0"
                    },
                    "openconfig-if-ethernet:ethernet": {
                        "config": {
                            "openconfig-if-aggregate:aggregate-id": "Arista"
                        }
                    }
                },
                {
                    "name": "1/1.0",
                    "config": {
                        "name": "1/1.0"
                    },
                    "openconfig-if-ethernet:ethernet": {
                        "config": {
                            "openconfig-if-aggregate:aggregate-id": "HA-Interconnect"
                        }
                    }
                },
                {
                    "name": "2/2.0",
                    "config": {
                        "name": "2/2.0"
                    },
                    "openconfig-if-ethernet:ethernet": {
                        "config": {
                            "openconfig-if-aggregate:aggregate-id": "HA-Interconnect"
                        }
                    }
                }
            ]
        }
    }

The final step is adding LACP configuration for each LAG:

.. code-block:: bash

  PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/

Enter the following in the body of the API request.

.. code-block:: json

  {
      "ietf-restconf:data": {
          "openconfig-lacp:lacp": {
              "interfaces": {
                  "interface": [
                      {
                          "name": "Arista",
                          "config": {
                              "name": "Arista",
                              "interval": "FAST",
                              "lacp-mode": "ACTIVE"
                          }
                      },
                      {
                          "name": "HA-Interconnect",
                          "config": {
                              "name": "HA-Interconnect",
                              "interval": "FAST",
                              "lacp-mode": "ACTIVE"
                          }
                      }
                  ]
              }
          }
      }
  }

To view the final LAG configuration via the API, use the following API call:

.. code-block:: bash

	GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-lacp:lacp

The output of the API call will be similar to the example below.

.. code-block:: json

    {
      "openconfig-lacp:lacp": {
          "config": {
              "system-priority": 32768
          },
          "state": {
              "f5-lacp:system-id-mac": "00:94:a1:8e:58:18"
          },
          "interfaces": {
              "interface": [
                  {
                      "name": "Arista",
                      "config": {
                          "name": "Arista",
                          "interval": "FAST",
                          "lacp-mode": "ACTIVE"
                      },
                      "state": {
                          "name": "Arista",
                          "interval": "FAST",
                          "lacp-mode": "ACTIVE",
                          "system-id-mac": "0:94:a1:8e:58:18"
                      },
                      "members": {
                          "member": [
                              {
                                  "interface": "1/2.0",
                                  "state": {
                                      "activity": "ACTIVE",
                                      "timeout": "SHORT",
                                      "synchronization": "IN_SYNC",
                                      "aggregatable": true,
                                      "collecting": true,
                                      "distributing": true,
                                      "system-id": "0:94:a1:8e:58:18",
                                      "oper-key": 2,
                                      "partner-id": "44:4c:a8:fc:cc:23",
                                      "partner-key": 11,
                                      "port-num": 4352,
                                      "partner-port-num": 469,
                                      "counters": {
                                          "lacp-in-pkts": "2481",
                                          "lacp-out-pkts": "2031",
                                          "lacp-rx-errors": "0",
                                          "lacp-tx-errors": "0",
                                          "lacp-unknown-errors": "0",
                                          "lacp-errors": "0"
                                      }
                                  }
                              },
                              {
                                  "interface": "2/1.0",
                                  "state": {
                                      "activity": "ACTIVE",
                                      "timeout": "SHORT",
                                      "synchronization": "IN_SYNC",
                                      "aggregatable": true,
                                      "collecting": true,
                                      "distributing": true,
                                      "system-id": "0:94:a1:8e:58:18",
                                      "oper-key": 2,
                                      "partner-id": "44:4c:a8:fc:cc:23",
                                      "partner-key": 11,
                                      "port-num": 8320,
                                      "partner-port-num": 457,
                                      "counters": {
                                          "lacp-in-pkts": "2498",
                                          "lacp-out-pkts": "2031",
                                          "lacp-rx-errors": "0",
                                          "lacp-tx-errors": "0",
                                          "lacp-unknown-errors": "0",
                                          "lacp-errors": "0"
                                      }
                                  }
                              }
                          ]
                      }
                  },
                  {
                      "name": "HA-Interconnect",
                      "config": {
                          "name": "HA-Interconnect",
                          "interval": "FAST",
                          "lacp-mode": "ACTIVE"
                      },
                      "state": {
                          "name": "HA-Interconnect",
                          "interval": "FAST",
                          "lacp-mode": "ACTIVE",
                          "system-id-mac": "0:94:a1:8e:58:18"
                      },
                      "members": {
                          "member": [
                              {
                                  "interface": "1/1.0",
                                  "state": {
                                      "activity": "ACTIVE",
                                      "timeout": "SHORT",
                                      "synchronization": "IN_SYNC",
                                      "aggregatable": true,
                                      "collecting": true,
                                      "distributing": true,
                                      "system-id": "0:94:a1:8e:58:18",
                                      "oper-key": 3,
                                      "partner-id": "0:94:a1:8e:d0:18",
                                      "partner-key": 3,
                                      "port-num": 4224,
                                      "partner-port-num": 8448,
                                      "counters": {
                                          "lacp-in-pkts": "2230",
                                          "lacp-out-pkts": "2030",
                                          "lacp-rx-errors": "0",
                                          "lacp-tx-errors": "0",
                                          "lacp-unknown-errors": "0",
                                          "lacp-errors": "0"
                                      }
                                  }
                              },
                              {
                                  "interface": "2/2.0",
                                  "state": {
                                      "activity": "ACTIVE",
                                      "timeout": "SHORT",
                                      "synchronization": "IN_SYNC",
                                      "aggregatable": true,
                                      "collecting": true,
                                      "distributing": true,
                                      "system-id": "0:94:a1:8e:58:18",
                                      "oper-key": 3,
                                      "partner-id": "0:94:a1:8e:d0:18",
                                      "partner-key": 3,
                                      "port-num": 8448,
                                      "partner-port-num": 4224,
                                      "counters": {
                                          "lacp-in-pkts": "2236",
                                          "lacp-out-pkts": "2030",
                                          "lacp-rx-errors": "0",
                                          "lacp-tx-errors": "0",
                                          "lacp-unknown-errors": "0",
                                          "lacp-errors": "0"
                                      }
                                  }
                              }
                          ]
                      }
                  }
              ]
          }
      }
  }

You can get more granular information down to the interface level using the following API command:

.. code-block:: bash

	GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-interfaces:interfaces

The output of the API call will be similar to the example below.

.. code-block:: json

  {
      "openconfig-interfaces:interfaces": {
          "interface": [
              {
                  "name": "1/1.0",
                  "config": {
                      "name": "1/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true
                  },
                  "state": {
                      "name": "1/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "91534528",
                          "in-unicast-pkts": "0",
                          "in-broadcast-pkts": "1",
                          "in-multicast-pkts": "715113",
                          "in-discards": "0",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "91515778",
                          "out-unicast-pkts": "0",
                          "out-broadcast-pkts": "0",
                          "out-multicast-pkts": "714971",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_UP"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "config": {
                          "openconfig-if-aggregate:aggregate-id": "HA-Interconnect"
                      },
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d0:02",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      }
                  }
              },
              {
                  "name": "1/2.0",
                  "config": {
                      "name": "1/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true
                  },
                  "state": {
                      "name": "1/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "124919687",
                          "in-unicast-pkts": "0",
                          "in-broadcast-pkts": "1869",
                          "in-multicast-pkts": "956957",
                          "in-discards": "0",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "91513088",
                          "out-unicast-pkts": "0",
                          "out-broadcast-pkts": "0",
                          "out-multicast-pkts": "714946",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_UP"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "config": {
                          "openconfig-if-aggregate:aggregate-id": "Arista"
                      },
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d0:03",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      }
                  }
              },
              {
                  "name": "2/1.0",
                  "config": {
                      "name": "2/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true
                  },
                  "state": {
                      "name": "2/1.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "115515500",
                          "in-unicast-pkts": "0",
                          "in-broadcast-pkts": "7873",
                          "in-multicast-pkts": "879353",
                          "in-discards": "0",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "91518344",
                          "out-unicast-pkts": "0",
                          "out-broadcast-pkts": "0",
                          "out-multicast-pkts": "715003",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_UP"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "config": {
                          "openconfig-if-aggregate:aggregate-id": "Arista"
                      },
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d0:82",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      }
                  }
              },
              {
                  "name": "2/2.0",
                  "config": {
                      "name": "2/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "enabled": true
                  },
                  "state": {
                      "name": "2/2.0",
                      "type": "iana-if-type:ethernetCsmacd",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "counters": {
                          "in-octets": "136475840",
                          "in-unicast-pkts": "0",
                          "in-broadcast-pkts": "702127",
                          "in-multicast-pkts": "715154",
                          "in-discards": "0",
                          "in-errors": "0",
                          "in-fcs-errors": "0",
                          "out-octets": "91515522",
                          "out-unicast-pkts": "0",
                          "out-broadcast-pkts": "0",
                          "out-multicast-pkts": "714969",
                          "out-discards": "0",
                          "out-errors": "0"
                      },
                      "f5-interface:forward-error-correction": "auto",
                      "f5-lacp:lacp_state": "LACP_UP"
                  },
                  "openconfig-if-ethernet:ethernet": {
                      "config": {
                          "openconfig-if-aggregate:aggregate-id": "HA-Interconnect"
                      },
                      "state": {
                          "port-speed": "openconfig-if-ethernet:SPEED_100GB",
                          "hw-mac-address": "00:94:a1:8e:d0:83",
                          "counters": {
                              "in-mac-control-frames": "0",
                              "in-mac-pause-frames": "0",
                              "in-oversize-frames": "0",
                              "in-jabber-frames": "0",
                              "in-fragment-frames": "0",
                              "in-8021q-frames": "0",
                              "in-crc-errors": "0",
                              "out-mac-control-frames": "0",
                              "out-mac-pause-frames": "0",
                              "out-8021q-frames": "0"
                          },
                          "f5-if-ethernet:flow-control": {
                              "rx": "on"
                          }
                      }
                  }
              },
              {
                  "name": "Arista",
                  "config": {
                      "name": "Arista",
                      "type": "iana-if-type:ieee8023adLag",
                      "enabled": true
                  },
                  "state": {
                      "name": "Arista",
                      "type": "iana-if-type:ieee8023adLag",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "f5-interface:forward-error-correction": "auto"
                  },
                  "openconfig-if-aggregate:aggregation": {
                      "config": {
                          "lag-type": "LACP",
                          "f5-if-aggregate:distribution-hash": "src-dst-ipport"
                      },
                      "state": {
                          "lag-type": "LACP",
                          "lag-speed": 200,
                          "f5-if-aggregate:distribution-hash": "src-dst-ipport",
                          "f5-if-aggregate:mac-address": "00:94:a1:8e:d0:09",
                          "f5-if-aggregate:lagid": 1
                      },
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  3010,
                                  3011
                              ]
                          }
                      }
                  }
              },
              {
                  "name": "HA-Interconnect",
                  "config": {
                      "name": "HA-Interconnect",
                      "type": "iana-if-type:ieee8023adLag",
                      "enabled": true
                  },
                  "state": {
                      "name": "HA-Interconnect",
                      "type": "iana-if-type:ieee8023adLag",
                      "mtu": 9600,
                      "enabled": true,
                      "oper-status": "UP",
                      "f5-interface:forward-error-correction": "auto"
                  },
                  "openconfig-if-aggregate:aggregation": {
                      "config": {
                          "lag-type": "LACP",
                          "f5-if-aggregate:distribution-hash": "src-dst-ipport"
                      },
                      "state": {
                          "lag-type": "LACP",
                          "lag-speed": 200,
                          "f5-if-aggregate:distribution-hash": "src-dst-ipport",
                          "f5-if-aggregate:mac-address": "00:94:a1:8e:d0:0a",
                          "f5-if-aggregate:lagid": 2
                      },
                      "openconfig-vlan:switched-vlan": {
                          "config": {
                              "trunk-vlans": [
                                  500,
                                  501,
                                  502,
                                  503
                              ]
                          }
                      }
                  }
              }
          ]
      }
  }

