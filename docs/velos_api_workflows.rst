===================
VELOS API Workflows
===================

There are three main points of management within the VELOS chassis: the system controllers, the chassis partitions, and the individual tenants. Each support their own CLI, webUI, and API access.

At the system controller level, initial configuration consists of defining static and floating IP addresses, optional Link Aggregation for the out-of-band management ports, routing, and other system parameters like DNS. Licensing is also configured at the system controller level and is similar to VIPRION in that it is applied at the chassis level and inherited by all chassis partitions and tenants.

For more information about configuring your system, see:

`VELOS Systems: Getting Started and VELOS Systems: Administration and Configuration <https://techdocs.f5.com/kb/en-us/products/f5os-c/manuals/related/doc-f5os-c-velos.html>`_

Once network connectivity to the system controller has been setup, you can then use the F5OS-C API to deploy the rest of the VELOS system. These workflows assume that the initial system controller configuration has been completed. You can also download the VELOS Postman collection, which has many of the API workflows below.

`VELOS F5OS-C Postman Collection <https://github.com/f5devcentral/f5-velos-training/raw/main/docs/postman/F5%20Postman%20for%20F5OS-C%20VELOS.zip>`_



Workflows
=========

Initial Setup of System Controllers
-----------------------------------


`Configure Internal Network Ranges via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#internal-chassis-ip-ranges-via-api>`_

`IP Address Assignment & Routing via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#ip-address-assignment-routing-via-api>`_

`Interface Aggregation for System Controllers via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#interface-aggregation-for-system-controllers-via-api>`_

`Configure Primary-Key via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#setting-the-primary-key-via-api>`_

`Configure System Settings From the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-dns-ntp-logging-from-the-api>`_

`Configure System Settings DNS From the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-dns-from-the-api>`_

`Configure System Settings NTP & Timezone From the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-ntp-timezone-from-the-api>`_

`Configure System Settings SYSLOG From the API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-syslog-from-the-api>`_

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


`Loading Tenant Images from a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#loading-tenant-images-from-a-remote-server-via-api>`_

`Uploading Tenant Images from a Client Machine via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#uploading-tenant-images-from-a-client-machine-via-the-api>`_

`Creating a Tenant via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#creating-a-tenant-via-api>`_

`Validating Tenant Status via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#validating-tenant-status-via-api>`_

`Expanding a Tenant within the Same Blade via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#expanding-a-tenant-within-the-same-blade-via-api>`_

`Expanding a Tenant Across Blades via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_deploying_a_tenant.html#expanding-a-tenant-across-blades-via-api>`_

Securing / Hardening F5OS on VELOS
----------------------------------

`Adding Allow List Entries via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#adding-allow-list-entries-via-api>`_

`Managing Device Certificates, Keys, CSRs, and CAs via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#managing-device-certificates-keys-csrs-and-cas-via-api>`_

`Enabling Appliance Mode via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#enabling-appliance-mode-via-the-api>`_

`Enabling BIG-IP Tenant Appliance Mode via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#enabling-big-ip-tenant-appliance-mode-via-the-api>`_

`Validating Tenant Status via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#validating-tenant-status-via-api>`_

`Resource-Admin & Guest User Role via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#resource-admin-guest-user-role-via-api>`_

`Superuser Role via API using Named Groups on LDAP/Active Directory <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#superuser-role-via-api-using-named-groups-on-ldap-active-directory>`_

`Configuring SSH and CLI Timeouts & Deny Root SSH Settings via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#configuring-ssh-and-cli-timeouts-deny-root-ssh-settings-via-api>`_

`Token Lifetime via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#token-lifetime-via-api>`_

`Disabling Basic Auth via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#disabling-basic-auth-via-the-api>`_

`Setting Password Policies via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#setting-password-policies-via-api>`_

`Configuring Login Banner / MoTD via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#configuring-login-banner-motd-via-api>`_

`Enabling NTP Authentication via API <https://clouddocs.f5.com/training/community/velos-training/html/initial_setup_of_velos_system_controllers.html#configure-system-settings-ntp-time-zone-from-the-api>`_

`Configuring Management Ciphers via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#configuring-management-ciphers-via-api>`_

`Configuring Client Certificate Authentication via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#configuring-client-certificate-authentication-via-api>`_

`Proxy Server via API for Licensing and Qkview Uploads to iHealth <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#proxy-server-via-api-for-licensing-and-qkview-uploads-to-ihealth>`_

`Downloading Audit Logs via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_security.html#downloading-audit-logs-via-api>`_

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

`Downloading System Controller Database Backup to a Client Machine via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#downloading-system-controller-database-backup-to-a-client-machine-via-api>`_

`Backing Up Chassis Partition Databases via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#backing-up-chassis-partition-databases-via-api>`_

`Export Backup From the Chassis Partition to a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#export-backup-from-the-chassis-partition-to-a-remote-server-via-api>`_

`Download Backup From the Chassis Partition to a Client Machine via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#download-backup-from-the-chassis-partition-to-a-client-machine-via-api>`_

`Remove Partitions and Reset Controller via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#remove-partitions-and-reset-controller-via-api>`_

`Importing System Controller Backups from a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#importing-system-controller-backups-from-a-remote-server-via-api>`_

`Uploading System Controller Backups from a Client Machine via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#uploading-system-controller-backups-from-a-client-machine-via-api>`_

`Restoring the System Controller from a Database Backup via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#restoring-the-system-controller-from-a-database-backup-via-api>`_

`Importing Archived Chassis Partition Configs form a Remote Server via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#importing-archived-chassis-partition-configs-form-a-remote-server-via-api>`_

`Uploading Archived Chassis Partition Configs form a Client Machine via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#uploading-archived-chassis-partition-configs-form-a-client-machine-via-api>`_

`Uploading System Controller Backups from a Client Machine via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#uploading-system-controller-backups-from-a-client-machine-via-api>`_

`Restoring Chassis Partitions from Database Backups via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_f5os_configuration_backup_and_restore.html#restoring-chassis-partitions-from-database-backups-via-api>`_


Diagnostics
-----------

`Qkview Creation and Upload to iHealth via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#qkview-creation-and-upload-to-ihealth-via-api>`_

`Qkview Download to Client via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#qkview-download-to-client-via-api>`_

`Downloading Logs from the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#downloading-logs-from-the-api>`_

`Viewing Event Logs from the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#viewing-event-logs-from-the-api>`_

`Adjusting Software Component Logging Levels via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#adjusting-software-component-logging-levels-via-api>`_

`TCPDUMP Download to Client via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_diagnostics.html#tcpdump-download-to-client-via-api>`_

Health
------

`Checking Active Alerts via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-active-alerts-via-api>`_

`Checking System Health via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-system-health-via-api>`_


Monitoring VELOS Health & Alert Status
---------------------------------------

`Checking Active Alerts via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-active-alerts-via-api>`_

`Checking System Health via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos_health_status.html#checking-system-health-via-api>`_




Monitoring
----------

`System Inventory / Components from the API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#system-inventory-components-from-the-api>`_

`System Alerts via API <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#system-alerts-via-api>`_

`API Monitoring of Chassis Cluster Status from the System Controller <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#api-monitoring-of-chassis-cluster-status-from-the-system-controller>`_

`API Monitoring of Chassis Partitions from the System Controller <https://clouddocs.f5.com/training/community/velos-training/html/monitoring_velos.html#api-monitoring-of-chassis-partitions-from-the-system-controller>`_


VELOS F5OS-A SNMP Monitoring and Alerting
-----------------------------------------

`Downloading MIBs via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#downloading-mibs-via-api>`_

`Exporting MIBs to a Remote Server via the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#exporting-mibs-to-a-remote-server-via-the-api>`_

`Adding Allowed IPs for SNMP via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#adding-allowed-ips-for-snmp-via-api>`_

`Adding Out-of-Band Interface and LAG descriptions via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#adding-out-of-band-interface-and-lag-descriptions-via-api>`_

`Adding Interface and LAG Descriptions for Chassis Partitions via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#adding-interface-and-lag-descriptions-for-chassis-partitions-via-api>`_

`Configuring SNMP Access via API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#enabling-snmp-via-api>`_

`Enabling SNMP Traps in the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#enabling-snmp-traps-in-the-api>`_

`Downloading SNMP Logs from the API <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html#downloading-snmp-logs-from-the-api>`_