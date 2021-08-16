=============================
Points of Management in VELOS
=============================

There are three main points of management within the VELOS chassis. The **system controllers**, the **chassis partitions**, and the individual **tenants**. Each support their own CLI, GUI, and API access and have their own authentications and users.

.. image:: images/velos_points_of_management/image1.png
  :align: center
  :scale: 40%

At the system controller level, initial configuration consists of static and floating IP addresses, optional link aggregation for the out-of-band management ports, routing and other system parameters like DNS & NTP. Licensing is also configured at the system controller level and is similar to VIPRION in that it is applied at the chassis level and inherited by all chassis partitions and tenants.  Below is an example of initial system controller configuration: Please refer to the VELOS Platform Guide on askf5.com for more detailed information.

  
