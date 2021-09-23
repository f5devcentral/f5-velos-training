=======================================
VELOS F5OS SNMP Monitoring and Alerting
=======================================

SNMP Support for F5OS will vary by release. In the intial v1.1.x versions of F5OS SNMP support is limited to SNMP Trap support from the System Controllers & Chassis Partitions, and IF-MIB support for the chassis partitions. F5OS v1.2.x added addtional SNMP support including Link Up/Down Traps for chassis partittions, and support for  IF-MIB, EtherLike-MIB, & the PLATFORM-STATS-MIB.


Enabling SNMP via CLI
=============================

Setting up SNMP can de done from the CLI by enabling an SNMP community. Below is an example of enabling SNMP monitroing on a chassis partition:

.. code-block:: bash

    bigpartition-2(config)# SNMP-COMMUNITY-MIB snmpCommunityTable snmpCommunityEntry boyapati snmpCommunityName boyapati snmpCommunitySecurityName boyapati
    bigpartition-2(config-snmpCommunityEntry-boyapati)# exit
    bigpartition-2(config)# SNMP-VIEW-BASED-ACM-MIB vacmSecurityToGroupTable vacmSecurityToGroupEntry 2 boyapati vacmGroupName read-access
    bigpartition-2(config-vacmSecurityToGroupEntry-2/boyapati)# exit
    bigpartition-2(config)# commit 
    Commit complete.
    bigpartition-2(config)# SNMP-VIEW-BASED-ACM-MIB vacmSecurityToGroupTable vacmSecurityToGroupEntry 1 boyapati vacmGroupName read-access
    bigpartition-2(config-vacmSecurityToGroupEntry-1/boyapati)# exit
    bigpartition-2(config)# commit 
    Commit complete.

You can configure the SNMP System paramters including the System Contact, System Location, and System Name as seen below:

.. code-block:: bash

    bigpartition-2(config)# SNMPv2-MIB system sysContact jim@f5.com sysLocation Boston sysName VELOS-bigpartition
    bigpartition-2(config)# commit



It is highly recommend that you put Interface descriptions in your configuration so that this will showup when using SNMP polling:

.. code-block:: bash

    bigpartition-2(config)# interfaces interface 1/1.0
    bigpartition-2(config-interface-1/1.0)# config description "Interface-1/1.0"
    bigpartition-2(config-interface-1/1.0)# exit                              
    bigpartition-2(config)# interfaces interface 1/2.0        
    bigpartition-2(config-interface-1/2.0)# config description Interface-1/2.0
    bigpartition-2(config-interface-1/2.0)# exit                              
    bigpartition-2(config)# interfaces interface 2/1.0        
    bigpartition-2(config-interface-2/1.0)# config description Interface-2/1.0
    bigpartition-2(config-interface-2/1.0)# exit
    bigpartition-2(config)# interfaces interface 2/2.0        
    bigpartition-2(config-interface-2/2.0)# config description Interface-2/2.0
    bigpartition-2(config-interface-2/2.0)# exit
    bigpartition-2(config)# commit
    Commit complete.


If LAGs are configured decriptions should be added to the LAG interfaces as well:

.. code-block:: bash

    bigpartition-2(config)# interfaces interface Arista 
    bigpartition-2(config-interface-Arista)# config description "Arista LAG"
    bigpartition-2(config-interface-Arista)# exit
    bigpartition-2(config)# interfaces interface HA-Interconnect 
    bigpartition-2(config-interface-HA-Interconnect)# config description "HA-Interconnect LAG"
    bigpartition-2(config-interface-HA-Interconnect)# exit
    bigpartition-2(config)# commit
    Commit complete.


Poling SNMP Endpoints
=====================


You can then poll the chassis partiton via SNMP to get stats from the system using the following SNMP OID's:

SNMP System OID: .1.3.6.1.2.1.1

Exmaple output:

.. code-block:: bash

    sysDescr.0	Linux 3.10.0-862.14.4.el7.centos.plus.x86_64 : Partition services version 1.2.1-10781	OctetString	10.255.0.148:161
    sysObjectID.0	system	OID	10.255.0.148:161
    sysUpTime.0	1 hour 13 minutes 13.88 seconds (439388)	TimeTicks	10.255.0.148:161
    sysContact.0	jim@f5.com	OctetString	10.255.0.148:161
    sysName.0	VELOS-bigpartition	OctetString	10.255.0.148:161
    sysLocation.0	Boston	OctetString	10.255.0.148:161
    sysServices.0	72	Integer	10.255.0.148:161
    .1.3.6.1.2.1.1.8.0	190 milliseconds (19)	TimeTicks	10.255.0.148:161
    .1.3.6.1.2.1.1.9.1.2.1	platform	OID	10.255.0.148:161
    .1.3.6.1.2.1.1.9.1.2.2	.1.3.6.1.2.1.31	OID	10.255.0.148:161

SNMP ifIndex OID: .1.3.6.1.2.1.2.2.1

+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| ifIndex   | ifDescr	          |  ifType	       | ifMtu |  ifSpeed	| ifPhysAddress	     | ifAdminStatus | ifOperStatus  | ifLastChange	 | ifInOctets | ifInUcastPkts | ifInNUcastPkts | ifInDiscards | ifInErrors | ifInUnknownProtos | ifOutOctets | ifOutUcastPkts | ifOutNUcastPkts | ifOutDiscards | ifOutErrors | ifOutQLen | ifSpecific | Index Value |
+===========+=====================+================+=======+============+====================+===============+===============+===============+============+===============+================+==============+============+===================+=============+================+=================+===============+=============+===========+============+=============+
| 33554441  | Interface-1/1.0	  | ethernetCsmacd | 9600  | 4294967295	| 00-94-A1-8E-D0-00  | up	         | up		     | 0	         | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| 33554442  | Interface-1/2.0	  | ethernetCsmacd | 9600  | 4294967295	| 00-94-A1-8E-D0-01	 | up	         | up		     | 0        	 | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| 33554449	| Interface-2/1.0	  | ethernetCsmacd | 9600  | 4294967295	| 00-94-A1-8E-D0-80	 | up            | up		     | 0	         | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| 33554450	| Interface-2/2.0	  | ethernetCsmacd | 9600  | 4294967295	| 00-94-A1-8E-D0-81	 | up            | up		     | 0	         | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| 67108865	| Arista LAG     	  | ieee8023adLag  | 9600  | 4294967295	| 00-94-A1-8E-D0-0B	 | up	         | up		     | 0	         | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+
| 67108866  | HA-Interconnect LAG |	ieee8023adLag  | 9600  | 4294967295	| 00-94-A1-8E-D0-0C	 | up            | up		     | 0	         | 0	      | 0	          | 0	           | 33554441     |            |                   |             |                |                 |               |             |           |            |             |
+-----------+---------------------+----------------+-------+------------+--------------------+---------------+---------------+---------------+------------+---------------+----------------+--------------+------------+-------------------+-------------+----------------+-----------------+---------------+-------------+-----------+------------+-------------+





Enabling SNMP Traps in the CLI
==============================




In F5OS v1.1.x:


- Controller - SNMP Traps
- Partition - SNMP Trps, IF-MIB walk


In F%OS 1.2.x:


- - Controller - SNMP Traps
Partition - SNMP Traps, linkUp/linkDown traps,
- walk support for IF-MIB, EtherLike-MIB, PLATFORM-STATS-MIB,






