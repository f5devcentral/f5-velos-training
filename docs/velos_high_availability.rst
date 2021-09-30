=======================
VELOS High Availability
=======================

System Controller HA
====================

The system controllers perform two main functions for the VELOS chassis. They are the centralized layer2 switch fabric connecting all blades within the system, and they also host the Kubernetes control plane which manages the F5OS layer. The CX410 system ships with redundant system controllers. 

The layer2 switch fabric function performed by the system controllers runs in an active-active manner. Both switch fabrics are active, and each BX110 blade is dual homed with a 100Gb backplane connection to each system controller (200Gb total). On the BX110 blade the two 100Gb ports are bonded together in a static Link Aggregation Group (LAG). Traffic destined for other blades in the system will hash over the two links (assuming both are active), then traverse the switch fabrics on the system controllers on its way to the destination blade. 


.. image:: images/velos_high_availability/image1.png
  :align: center
  :scale: 70%

While both switch fabrics are active there is 1.6Tbs of switching capacity between all the blades in the system. If one of the switch fabrics should fail, then the total bandwidth will be cut in half to 800Gbs on the backplane and each blade will be limited to 100Gbs of backplane capacity. Note that the current throughput rating on the BX110 blades (95Gb) will not push the backplane to full capacity.

The second function the system controllers perform is the management of the new Kubernetes platform layer. At this layer the system controllers run in an active/standby fashion. One system controller will be designated primary/active, and the other will be designated secondary/standby. All Kubernetes services will be active on the primary including the API server, scheduler, controller manager, etcd, GUI services etc…. The active system controller can always be reached via the floating IP address that is assigned to the system controllers. The floating address will move in the case of a controller failure. The secondary controller operates in a read-only manner and any changes to configuration must be made on the primary. All changes are replicated from primary to secondary controller, so they should always be in sync, there is no need to manually sync them. The Kubernetes control plane is responsible for deploying workloads on the BX110 blades.

.. image:: images/velos_high_availability/image2.png
  :align: center
  :scale: 70%

You may view the current high availability status in the dashboard of the system controller GUI. It will show which controllers is primary and standby, the preferred node and status.

.. image:: images/velos_high_availability/image3.png
  :align: center
  :scale: 70%

You can view and configure the High Availability options for the system controllers in the GUI **Systems Settings > Controller Management** screen. You can force a failover, configure auto failback, as well as set the preferred node:

.. image:: images/velos_high_availability/image4.png
  :align: center
  :scale: 70%

Failover Behavior
=================

The failover behavior will depend on the type of outage encountered at the system controller. You can perform a manual failover from one system controller to the other via the system controller GUI:

.. image:: images/velos_high_availability/image5.png
  :align: center
  :scale: 70%

This will fail the Kubernetes control plane services from the active system controller to the standby, and the floating IP address will move to the new active controller. This type of outage will be non-disruptive to client traffic flowing through the tenants and layer2 switch fabrics running on the system controllers which will continue to run in an active/active manner. Any management connections to the system controller or chassis partition F5OS GUI, CLI, or API’s or will be disconnected, and will have to be re-established through the new active system controller. 

A physical reboot of the active system controller will cause all backplane traffic to temporarily use only the previously standby system controller. In the initial 1.1.x versions of F5OS tenant traffic may be disrupted during the reboot process as the system controllers shut down and reload. 

If a software upgrade is being performed, then both system controllers are upgraded in parallel resulting in an outage for the entire chassis for the initial 1.1.x versions of F5OS. Rolling upgrade support for the system controllers has been added in the F5OS 1.2.x release. This will allow for a serial rolling upgrade to take place for the system controllers and minimal downtime to the tenants when controllers are upgraded.


Blade Level HA
==============

From a design standpoint it is best to spread in-band network connections across multiple blades if possible. Assuming there is more than one blade within a chassis partition spreading network connections via LAG’s will give the best resiliency and performance. Consider the diagram below where a LAG is split across two different BX110 blades. Incoming traffic should hash across both links and statistically should provide fairly even distribution given enough connections. Performance can still be lumpy, as the upstream switch is likely performing a hash-based distribution, and not distributing based on load. 

Incoming traffic will go through a disaggregation (DAG) process where connections are spread across all processors within the VELOS tenant. Again, a hash-based distribution will decide which blade/processor should handle the incoming connection. Given enough connections the expectation is that half are processed locally on the incoming blade, and the other half will be sent across the backplane to another blade assuming a tenant is configured to utilize more than one blade. If the return traffic going outbound can egress on the same blade that processed the connection, then a backplane traversal is saved, it doesn’t have to go back to the incoming blade. If a blade fails, or one of the links in the LAG should fail, then all traffic will ingress and egress on the remaining blade. There are more granular configuration options within the tenant to determine how failover cases should be handled if a blade should fail. Of course, additional blades/links can be added to a chassis partition, but they follow this forwarding behavior:

.. image:: images/velos_high_availability/image6.png
  :align: center
  :scale: 50%

This approach is better than terminating a LAG on a single blade. Incoming connections will DAG in a similar manner as described above; however, all egress traffic will only be able to go out on the same blade as where it initially ingresses. This will cause an extra backplane traversal. The LAG will provide link redundancy, but if the blade fails, there is no redundancy within the chassis to deal with this, you’ll need to configure HA groups or some other failover mechanism in the tenant to fail over to the remaining chassis. 

.. image:: images/velos_high_availability/image7.png
  :align: center
  :scale: 50%

Tenant Level HA Across Chassis
==============================

VELOS does not support tenant HA within the same chassis. F5 recommends configuring dual VELOS chassis with identically configured tenants and maintaining HA relationships at the tenant level as seen below. This mimics the VIPRION HA behavior between vCMP guests. There is no redundancy between chassis at the F5OS platform layer. The chassis’ themselves are unaware of the other chassis and there is no HA communication at this level, it’s the tenants that form the HA relationship.

.. image:: images/velos_high_availability/image8.png
  :align: center
  :scale: 70%

Tenants on different chassis, should have the same number of vCPU’s and be configured to run on the same slots. HA interconnection VLANs would be configured between chassis partitions in the two chassis, and then tenants would configure HA just as is the case with vCMP guest HA relationships. Below is an example of two VELOS chassis with multiple chassis partitions each with their own HA interconnects and in-band networking.

.. image:: images/velos_high_availability/image9.png
  :align: center
  :scale: 70%

Tenant Level HA within the Chassis
==================================

VELOS does not support configuring HA relationships between tenants within the same chassis. You can configure tenants to span multiple blades, and then depending on what failover behavior you want, you can have the tenant run with less capacity within the same chassis if a blade fails or fail over to the tenant in the other chassis. This is controlled within the tenant itself, just like vCMP guests HA failover was configured. HA groups allow an administrator to fail over based on pool, trunk, or blade availability. 

**Note: The HA Groups failover option based on trunks is not supported in VELOS tenants running 14.1.4.x and F5OS v1.1.x. The Tenants do not have visibility into the F5OS layer Link Aggregation Groups. F5OS 1.2.x and later along with VELOS tenant running 15.1.4 or later support trunks within an HA Group.**

**NOTE: Expanding a tenant across two or more blades will require additional configuration and IP addresses within the tenant. A tenant will require one out-of-band IP address for each slot it is hosted on plus one floating IP address. This is similar to how vCMP addressing is configured for HA.**

Below is an example of a “SuperVIP” tenant that spans all 8 blades. Each system controller will have one static IP address, and then there is a floating IP address. The chassis partition will require an out-of-band management address, as will the tenant. If the chassis partition is comprised of all 8 blades and the tenant will be spread across all 8 blades, then the tenant will need to have an IP address configured for each blade for proper HA failover and synchronization.


.. image:: images/velos_high_availability/image10.png
  :align: center
  :scale: 70%

Inside the tenant, one **Cluster Member IP Address** will need to be configured for each blade. If using IPv4 & IPv6 (dual stack management) then **Alternate Management** & **Cluster Member IP addresses** can be configured.

.. image:: images/velos_high_availability/image11.png
  :align: center
  :scale: 90%

For planning purposes a single large tenant “SuperVip” spanning 8 total blades would require 13 out-of-band management IP addresses for each chassis. In-band Self-IP & Virtual addresses are not included in this calculation.

+------------------------------+----------------------------------+--------------------+
| **IP Addresses Required**    | **Single Chassis**               | **HA Environment** | 
+==============================+==================================+====================+
| System Controller 1          |     1                            |  x2 for HA = 2     |
+------------------------------+----------------------------------+--------------------+
| System Controller 2          |     1                            |  x2 for HA = 2     | 
+------------------------------+----------------------------------+--------------------+
| System Controller Floating   |     1                            |  x2 for HA = 2     |
+------------------------------+----------------------------------+--------------------+
| Chassis Partition            |     1 per Chassis Partition      |  x2 for HA = 2     | 
+------------------------------+----------------------------------+--------------------+
| Tenant Management            |     1 per Tenant                 |  x2 for HA = 2     | 
+------------------------------+----------------------------------+--------------------+
| Tenant Cluster Member        |     8 per Tenant (one per slot)  |  x2 for HA = 16    |
+------------------------------+----------------------------------+--------------------+
| Total                        |     13                           |  x2 for HA = 26    | 
+------------------------------+----------------------------------+--------------------+

HA Group Configuration to Control Tenant Failover
=================================================

An active tenant will naturally failover to the standby tenant in another VELOS chassis if the tenant is not healthy and the standby detects it has failed. Ideally you should also configure HA Groups or some other mechanism within the tenant to detect external conditions that you will want to trigger a failover for. As an example, HA groups can monitor blades in the VELOS chassis, and failover if a minimum number of active blades is not met. You can also monitor pool member reachability to trigger a failover.

**Note: The HA Groups failover option based on trunks is not supported in F5OS v1.1.x versions with VELOS tenants running 14.1.4.x. The tenants do not have visibility into the F5OS layer Link Aggregation Groups. F5OS v1.2.x and later along with tenants running 15.1.4 or later support this functionality.**
