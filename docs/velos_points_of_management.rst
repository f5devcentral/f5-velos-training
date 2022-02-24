=============================
Points of Management in VELOS
=============================

There are three main points of management within the VELOS chassis. The **system controllers**, the **chassis partitions**, and the individual **tenants**. Each support their own CLI, webUI, and API access and have their own authentications and users. Additionally, they each run their own version of software; tenants are able to run specific versions of TMOS which have been approved to run on the VELOS platform, and system controllers and chassis partitions each have their own version of F5OS.

.. image:: images/velos_points_of_management/image1.png
  :align: center
  :scale: 40%

At the system controller level, initial configuration consists of static and floating IP addresses, optional link aggregation for the out-of-band management ports, routing and other system parameters like DNS & NTP. Licensing is also configured at the system controller level and is similar to VIPRION in that it is applied at the chassis level and inherited by all chassis partitions and tenants.  Please refer to the **VELOS Systems Administration Guide** on askf5.com for more detailed information.

https://techdocs.f5.com/en-us/velos-1-1-0/velos-systems-administration-configuration.html



  
