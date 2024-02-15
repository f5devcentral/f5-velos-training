===================
VELOS API Workflows
===================

There are three main points of management within the VELOS chassis: the system controllers, the chassis partitions, and the individual tenants. Each support their own CLI, webUI, and API access.

At the system controller level, initial configuration consists of defining static and floating IP addresses, optional Link Aggregation for the out-of-band management ports, routing, and other system parameters like DNS. Licensing is also configured at the system controller level and is similar to VIPRION in that it is applied at the chassis level and inherited by all chassis partitions and tenants.

For more information about configuring your system, see VELOS Systems: Getting Started and VELOS Systems: Administration and Configuration at support.f5.com.

These workflows assume that the initial system controller configuration has been completed and a first chassis partition is ready to be created.

Workflows
=========

Initial Setup of System Controllers
-----------------------------------

`Configure System Settings From the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-from-the-api>`_

`Manual Licensing via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#manual-licensing-via-api>`_

`Automatic Licensing via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#automatic-licensing-via-api>`_

Creating Chassis Partitions
---------------------------

`Creating a Chassis Partition via the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#creating-a-chassis-partition-via-the-api>`_

Configuring Networking
----------------------

`Configuring PortGroups from the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_chassis_partitions.html#configuring-portgroups-from-the-api>`_

`Configuring Interfaces from the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_chassis_partitions.html#configuring-interfaces-from-the-api>`_

`Configuring VLANs from the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_chassis_partitions.html#configuring-vlans-from-the-api>`_

`Configuring LAGs from the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_chassis_partitions.html#configuring-lags-from-the-api>`_

Tenant Deployments
------------------


`Loading Tenant Images from a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/rseries_deploying_a_tenant.html#loading-tenant-images-from-a-remote-server-via-api>`_

`Uploading Tenant Images from a Client Machine via the API <https://clouddocs.f5.com/training/community/velos-training/html/rseries_deploying_a_tenant.html#uploading-tenant-images-from-a-client-machine-via-the-api>`_

`Creating a Tenant via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#creating-a-tenant-via-api>`_

`Validating Tenant Status via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#validating-tenant-status-via-api>`_

`Expanding a Tenant within the Same Blade via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#expanding-a-tenant-within-the-same-blade-via-api>`_

`Expanding a Tenant Across Blades via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#expanding-a-tenant-across-blades-via-api>`_

F5OS-C Software Upgrades
------------------------

`Uploading Controller and Partition Images from a Remote Server via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#uploading-controller-and-partition-images-from-a-remote-server-via-the-api>`_

`Uploading Controller and Partition Images from a Client Machine via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#uploading-controller-and-partition-images-from-a-client-machine-via-the-api>`_

`Upgrading the System Controllers via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#upgrading-the-system-controllers-via-api>`_

`Upgrading a Chassis Partition via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#upgrading-a-chassis-partition-via-the-api>`_

`Loading Tenant Images from a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#loading-tenant-images-from-a-remote-server-via-api>`_

`Uploading Tenant Images from a Client Machine via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_software_upgrades.html#uploading-tenant-images-from-a-client-machine-via-the-api>`_

F5OS-C Backup and Restore
-------------------------

`Backing Up the System Controller Database via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#backing-up-the-system-controller-database-via-api>`_

`Copying System Controller Database Backup to an External Location via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#copying-system-controller-database-backup-to-an-external-location-via-api>`_

`Backing Up Chassis Partition Databases via API <https://clouddocs.f5.comtraining/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#backing-up-chassis-partition-databases-via-api>`_

`Export Backup From the Chassis Partition API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#export-backup-from-the-chassis-partition-api>`_

`Remove Partitions and Reset Controller via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#remove-partitions-and-reset-controller-via-api>`_

`Importing System Controller Backups via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#importing-system-controller-backups-via-api>`_

`Restoring the System Controller from a Database Backup via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#restoring-the-system-controller-from-a-database-backup-via-api>`_

`Importing Archived Chassis Partition Configs via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#importing-archived-chassis-partition-configs-via-api>`_

`Restoring Chassis Partitions from Database Backups via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#restoring-chassis-partitions-from-database-backups-via-api>`_

F5OS-C Diagnostics
------------------

`Generating Qkviews from the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#generating-qkviews-from-the-api>`_

Health
------

`Checking Active Alerts via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-active-alerts-via-api>`_

`Checking System Health via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-system-health-via-api>`_

Monitoring
----------

`System Inventory / Components from the API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#system-inventory-components-from-the-api>`_

`System Alerts via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#system-alerts-via-api>`_

`API Monitoring of Chassis Cluster Status from the System Controller <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#api-monitoring-of-chassis-cluster-status-from-the-system-controller>`_

`API Monitoring of Chassis Partitions from the System Controller <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#api-monitoring-of-chassis-partitions-from-the-system-controller>`_
