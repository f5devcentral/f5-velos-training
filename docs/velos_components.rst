================
VELOS Components
================

CX410 Chassis
=============

The CX410 is a 4RU chassis, that has eight ¼ width slots for the BX110 line cards, as well as 2 larger slots for SX410 system controllers. The system controllers proxy console and out-of-band Ethernet management for all the blades. The DC power version of VELOS is Network Equipment Building Systems (NEBS) compliant. NEBS standards are utilized all over the world for a host of commercial, utility, and defense applications.  The standards are designed to ensure that the equipment continues to work at extremes of temperature, or after an extreme event, like an earthquake or a severe thunderstorm.  

.. image:: images/image11.png

Rack Mounting the Chassis
=========================

An ideal environment for VELOS is to use a minimum of a 30” rack. The rackmount kits that ship with the system will support the minimum depth of 30” and can telescope to longer depths if needed. If the rack depth is less than 30” then custom rack mount kits will need to be ordered.   

.. image:: images/image12.png

Power Supplies & Controllers
============================

The VELOS CX410 ships with 2 power supplies. Additional power supplies can be added to provide N+1 or N+N redundancy. The initial version of VELOS supports AC power, and up to 4 power supplies (each with their own input) can be installed within the chassis. A DC power version of the chassis will be available in mid-CY21. Power supplies are removable from the front of the unit, by removing the LCD panel assembly. 

.. image:: images/image13.png

.. image:: images/image14.png

In the back of the chassis are 4 separate AC power inputs, and 2 redundant PSU controllers which are removable.

.. image:: images/image15.png

The PSU controllers are accessible in the upper left-hand corner of the back of the system.

.. image:: images/image16.png

.. image:: images/image17.png

A DC power version of the CX410 chassis will be made available mid CY21. 

.. image:: images/image18.png

Fan Tray & Cooling
===================

The VELOS chassis implements front-to-back cooling/airflow, and it is recommended that customers install such that VELOS is not intaking hot airflow from other devices. The fan tray is removable if its needs to be replaced but should not be removed for long periods of time, as overheating may occur. 

.. image:: images/image19.png

.. image:: images/image20.png

.. image:: images/image21.png

LCD Panel
==========

In the initial release of VELOS most of the LCD functionality has not been implemented yet. The LCD will go through self-tests, and display the product name and F5 logo, but no diagnostics or configuration is supported at this time. The LCD assembly can be removed to gain access to the power supplies if needed. 

.. image:: images/image22.png

.. image:: images/image23.png

SX410 System Controller
========================

Each CX410 chassis ships with two SX410 system controllers already installed. They are not optional, and are not ordered separately. The system controllers perform two main functions:

•	They provide the active backplane connectivity and layer2 switching to all line cards (BX110)
o	They operate in an active/active manner from a layer2 switching perspective
•	They host the Kubernetes control plane functions
o	They operate in an active/standby manner for these functions

It is recommended that a system always operate with two system controllers for redundancy. If one should fail the remaining system controller can takeover, however backplane capacity will drop from 1.6Tbps to 800Gbps. The Kubernetes control plane will run on the active system controller and will fail over to the standby if the active should fail. 

.. image:: images/image24.png

All out-of-band management and console access for the chassis is proxied through the system controllers. There is no need to cable these connections to the blades themselves as was the case with VIPRION. The BX110 blades have no console or out-of-band ethernet ports, it’s now centralized on the system controllers.

.. image:: images/image25.png

Looking at the left-hand side of the diagram below, you’ll notice the system controllers provide console and out-of-band management access to the chassis. This is proxied through the controller to the individual line cards. The system controllers have a built-in terminal server function that allows direct connection to a blade if required. Each system controller also acts as a centralized switch fabric interconnecting all blades in the system. Note there are 2 100Gb backplane connections (primary and secondary) to each slot in the system, but only the primary connection is used with the current generation of BX110 blades.

.. image:: images/image26.png

BX110 Blade
===========

The BX110 blade is a next generation data plane/line card. It has 2 high speed (QSFP+/QSFP28) ports that can be configured for 40Gb or 100Gb when bundled. If unbundled, then each port can be 4 x 25Gb or 4 x 10Gb with the proper breakout cable and optics installed. There are no direct console or out-of-band connections to the blade as those functions are now proxied by the system controllers. 

.. image:: images/image27.png

The BX110 has 14 physical cores which are hyperthreaded into 28 vCPU’s. 6 of the vCPU’s are reserved for the platform layer leaving 22vCPU’s available for multitenancy. Each blade comes with a 1TB SSD drive and is populated with 128GB of RAM (double the current generation VIPRION B2250). Each BX110 has two FPGA’s which provide hardware offload for certain functions. The Application Traffic Service Engine (ATSE) is the “front panel FPGA” which does initial classifications and offload, while the VELOS Queuing FPGA (VQF) is the “back panel FPGA” that implements queuing and CoS through the chassis backplane. The CPU complex provides hardware offload for SSL/TLS and compression similar to how previous generations of BIG-IP such as iSeries and VIPRION B4450 performed these operations, but with a newer generation of processor. 

.. image:: images/image28.png

The SSD is removable, but not field replaceable. This allows customers who require that disks are destroyed before returning a blade for RMA have easy access to the SSD.

.. image:: images/image29.png












