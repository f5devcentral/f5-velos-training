=============================
Points of Management in VELOS
=============================

There are three main points of management within the VELOS chassis: the **system controllers**, the **chassis partitions**, and the individual **tenants**. Each supports their own CLI, webUI, and API access and have their own authentications and users. 

.. image:: images/velos_points_of_management/image1.png
  :align: center
  :scale: 90%


Additionally, they each run their own version of software; tenants are able to run specific versions of TMOS which have been approved to run on the VELOS platform, and system controllers and chassis partitions each have their own version of F5OS-C software. The supported TMOS tenant versions are; 14.1.4 and later, 15.1.4 and later, and eventaully version 17.1.x and later. There are no plans to support version any 16.x versions, and there are no plans to support versions prior to 14.1.4. The F5OS-C platform layer in VELOS runs its own version of F5OS, which is unique to the VELOS chassis. On downloads.f5.com, the VELOS versions of F5OS are referred to as F5OS-C, where the C stands for chassis. The rSeries appliances also run F5OS, but that version is designated as F5OS-A, where A stands for appliance. Most of the code and configration interfaces of F5OS are common between VELOS and rSeries, but VELOS has unique F5OS features that are chassis specific. VELOS has two layers of F5OS (system controller and chassis partition), and each of these have their own software images, in addtion to the tenants that run TMOS.


.. image:: images/velos_points_of_management/image2.png
  :align: center
  :scale: 90%

At the F5OS-C platform layer, initial configuration consists of out-of-band management IP addresses, routing, and other system parameters like DNS and NTP. Licensing, is also configured at the F5OS layer, and is similar to VIPRION with vCMP configured, in that it is applied at the chassis level and inherited by all tenants. All of these items are configured at the system controller layer. The administrator also configures **chassis partitions** (groupings of VELOS blades/slots), that each have their own management IP address, and CLI, GUI, and API interfaces.

Inside the chassis partition F5OS layer, interfaces, in-band networking (VLANs, interfaces, Link Aggregation Groups) are configured. Once networking is set up, tenants can be provisioned, and deployed from the F5OS chassis partition management interfaces. Once the tenant is deployed, it is managed like any other BIG-IP instance. This is very similar to how vCMP guests are managed on iSeries or VIPRION. Please refer to the **VELOS Systems Administration Guide** on askf5.com for more detailed information.

https://techdocs.f5.com/en-us/velos-1-3-0/velos-systems-administration-configuration.html





  
