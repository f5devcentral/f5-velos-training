=====================================
F5OS Configuration Backup and Restore
=====================================

To completely backup the VELOS system, you’ll need to backup each tenant TMOS configuration, each F5OS chassis partition configuration, as well as the F5OS system controller configuration. Tenant backup utilizes the same backup and recovery procedures as existing BIG-IP devices/guests because the tenants themselves are running TMOS. For the F5OS layer (system controllers and chassis partitions) a different backup mechanism is utilized because F5OSc configuration management is based on ConfD.  

The confd process manages the F5OS configuration on a VELOS system. The system stores the configuration in its configuration database (CDB). There are separate Confd databases for the system controller layer, and for each chassis partition.

At the chassis level, the F5OS configuration contains data that includes the following:

•	DNS
•	Network time protocol (NTP)
•	Authentication servers
•	Logging setup
•	High availability (HA) setup
•	Management IP addresses of the system controllers
•	Product license
•	Basic configuration of existing partitions, such as the partition name, floating management IP address, and the partition-to-slot mapping

At the chassis partition level, the F5OS configuration contains data that includes the following:

•	Portgroups of the assigned slots/nodes/blades
•	Virtual Local Area Networks (VLANs)
•	Logging setup
•	Authentication servers
•	Product license
•	HA setup

To perform a complete backup of the VELOS system, you must:

•	Back up the configuration data at the chassis and at each chassis partition.
•	Back up any deployed tenants using the tenants’ backup mechanism (i.e. a UCS).

More detail is covered in the following solution article:

https://support.f5.com/csp/article/K50135154

Backing Up the System Controller Database
=========================================

**Using the CLI:**

You can back up the system controller configuration database using the **system database config-backup** command when in config mode. The file will be saved in the path of **/configs** automatically. You can then list the contents of that directory to ensure the file is there using the **file list path** command.

.. code-block:: bash

    syscon-1-active# config
    Entering configuration mode terminal
    syscon-1-active(config)# system database config-backup name chassis2-sys-controller-backup-2-26-21
    response Succeeded.
    syscon-1-active(config)# exit 

    syscon-1-active# file list path configs
    entries {
        name 
    chassis2-sys-controller-backup-2-26-21
    test-backup
    }
    syscon-1-active# 



**Using the GUI:**

Using the system controller GUI you can backup the confd configuration database using the **System Settings -> Configuration Backup** page. Click the **Create** button and provide a name for the backup file.

.. image:: images/velos_f5os_configuration_backup_and_restore/image1.png
   :width: 45%

.. image:: images/velos_f5os_configuration_backup_and_restore/image2.png
   :width: 45%

**Note: In the current F5OS releases the confd system database can be backed up via CLI/GUI/API but it cannot be restored using the F5OS GUI. This will be added in a subsequent release.**

Copying System Controller Database Backup to an External Location
=================================================================

Once the database backup has been completed, you should copy the file to an external location so that the system can be restored in the case of a total failure. You can download the database configuration backup using the CLI, GUI, or API. 

In the GUI use the **System Settings -> File Utilities** page and from the dropdown select **configs** to see the previously saved backup file. Here you can import or export configuration files. Note that the current transfer of files to and from the GUI requires an external HTTPS server. 

.. image:: images/velos_f5os_configuration_backup_and_restore/image3.png
  :align: center
  :scale: 70%

.. image:: images/velos_f5os_configuration_backup_and_restore/image4.png
  :align: center
  :scale: 70%

**Note: In the current release the exporting and importing the system database requires an external HTTPS server. Future releases will add more options for import/export that don’t rely on an external HTTPS server.**

To transfer a file using the CLI use the file list command to see the contents of the /configs directory. Note the previously saved file is listed.

.. code-block:: bash

    syscon-2-active# file list path configs/
    entries {
        name 
    chassis2-sys-controller-backup-2-26-21
    }
    syscon-2-active# 

To transfer the file from the CLI you can use the file export command. 

.. code-block:: bash

    syscon-1-active# file export local-file configs/chassis2-sys-controller-backup-2-26-21 remote-host 10.255.0.142 remote-file /backup username jim insecure 
    Value for 'password' (<string>): ***
    result File transfer is initiated.(configs/chassis2-sys-controller-backup-2-26-21)

To check on status of the export use the file transfer-status command:

.. code-block:: bash

    syscon-1-active# file transfer-status                                                                                                                                   
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            
    1    |Export file|HTTPS   |/mnt/var/confd/configs/chassis2-sys-controller-backup-2-26-21|10.255.0.142        |/backup                                                     |Method Not Allowed, HTTP Error 405
    2    |Export file|HTTPS   |/mnt/var/confd/configs/chassis1-sys-controller-backup-2-26-21|10.255.0.142        |chassis1-sys-controller-backup-2-26-21                      |Failed to open/read local data from file/application
    3    |Export file|HTTPS   |/mnt/var/confd/configs/chassis1-sys-controller-backup-2-26-21|10.255.0.142        |/backup                                                     |Failed to open/read local data from file/application

If you don’t have an external HTTPS server that allows uploads, then you can log into the system controllers floating IP address with root access and scp the file from the shell. Go to the **/var/confd/configs** directory and scp the file to an external location. Note in the cli and GUI the path is simplified to configs, but in the underlying file system it is actually stored in the /var/confd/confd directory.

.. code-block:: bash

    [root@controller-2 ~]# ls /var/confd/configs/
    controller-backup-08-17-21  my-backup
    [root@controller-2 ~]# scp /var/confd/configs/controller-backup-08-17-21 root@10.255.0.142:/var/www/server/1
    The authenticity of host '10.255.0.142 (10.255.0.142)' can't be established.
    ECDSA key fingerprint is SHA256:xexN3pt/7xGgGNFO3Lr77PHO2gobj/lV6vi7ZO7lNuU.
    ECDSA key fingerprint is MD5:ff:06:0f:a8:5f:64:92:7b:42:31:aa:bf:ea:ee:e8:3b.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.255.0.142' (ECDSA) to the list of known hosts.
    root@10.255.0.142's password: 
    controller-backup-08-17-21                                                       100%   77KB  28.8MB/s   00:00    
    [root@controller-2 ~]# 

Backing Up Chassis Partition Databases
======================================

In addition to backing up the system controller database, you should backup the configuration database on each chassis partition within the VELOS system. In the example below there are two chassis partitions currently in use; **bigpartition** and **smallpartition**. Both must be backed up and archived off of the VELOS system.

Log directly into the chassis partition bigpartitions management IP address and enter config mode. Use the **system database config-backup** command to save a copy of the chassis partitions config database. Then list the file using the **file list** command.

.. code-block:: bash

    bigpartition-1# config
    Entering configuration mode terminal
    bigpartition-1(config)# system database config-backup name chassis-partition-bigbartition-08-17-2021
    result Database backup successful.
    bigpartition-1(config)# exit
    bigpartition-1# file list path configs/
    entries {
        name 
    chassis-partition-bigbartition-08-17-2021
    }
    bigpartition-1# 


Log directly into the chassis partition smallpartition's management IP address and enter **config** mode. Use the **system database config-backup** command to save a copy of the chassis partitions config database. Then list the file using the **file list** command.

.. code-block:: bash

    smallpartition-1# config
    Entering configuration mode terminal
    smallpartition-1(config)# system database config-backup name chassis-partition-smallpartition-08-17-2021
    result Database backup successful.
    smallpartition-1(config)# exit
    smallpartition-1# file list path configs/
    entries {
        name 
    chassis-partition-smallpartition-08-17-2021
    }
    smallpartition-1# 


Next copy the backup files to a location outside of VELOS. The file can be copied off via the chassis partitions CLI, GUI, or API. In the current release you need an external HTTPS server configured to allow uploads in order to export database backups from VELOS. 

Export Backup From the Chassis Partition GUI
--------------------------------------------

You can copy the backup file out of the chassis partition using the **Systems Settings > File Utilities** menu in the GUI. Use the Base Directory drop down menu to select /configs directory, and you should see a copy of the file created there:

.. image:: images/velos_f5os_configuration_backup_and_restore/image5.png
  :align: center
  :scale: 70%

Note: In the current release the exporting and importing the system database requires an external HTTPS server. Future releases will add more options for import/export that don’t rely on an external HTTPS server.

Export Backup From the Chassis Partition CLI
--------------------------------------------

To transfer a file using the CLI use the **file list** command to see the contents of the configs directory. Note the previously saved file is listed. You will need to do this for all chassis partitions.

To backup chassis partition bigpartition:

.. code-block:: bash

    bigpartition-1# file list path configs/
    entries {
        name 
    chassis-partition-bigpartition-08-17-2021
    }
    bigpartition-1# 

To transfer the file from the CLI you can use the **file export** command. Note that the file export command requires a remote HTTPS server that the file can be posted to. 

.. code-block:: bash

    bigpartition-1# file export local-file configs/3-20-2021-bigpartition-backup remote-host 10.255.0.142 remote-file /backup/3-20-2021-bigpartition-backup username jim insecure 
    Value for 'password' (<string>): ***
    result File transfer is initiated.(configs/3-20-2021-bigpartition-backup)

You can use the CLI command **file transfer-status** to see if the file was copied successfully or not:

.. code-block:: bash

    bigpartition-1# file transfer-status 
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            
    1    |Export file|HTTPS   |/configs/3-20-2021-bigpartition-backup     |10.255.0.142        |/backup                                                     |Method Not Allowed, HTTP Error 405

    bigpartition-1# 

If you do not have a remote HTTPS server with the proper access to POST files then you can copy the chassis partition backups from the system controller shell. You’ll need to login to the system controllers shell using the root account. Once logged in list the contents of the **/var/F5** directory. You’ll notice partition<ID> directories, where <ID> equals the ID assigned to each partition.

.. code-block:: bash

    [root@controller-2 ~]# ls -al /var/F5/
    total 36
    drwxr-xr-x. 10 root root 4096 Mar 10 21:43 .
    drwxr-xr-x. 40 root root 4096 Mar  3 04:17 ..
    drwxr-xr-x.  3 root root 4096 Feb  8 19:58 controller
    drwxr-xr-x.  5 root root 4096 Feb  8 19:58 diagnostics
    drwxr-xr-x.  2 root root 4096 Feb  8 19:58 fips
    drwxr-xr-x. 24 root root 4096 Mar  3 04:27 partition1
    drwxr-xr-x.  3 root root   20 Mar 10 17:54 partition2
    drwxr-xr-x. 24 root root 4096 Mar  4 15:52 partition3
    drwxr-xr-x. 22 root root 4096 Mar 10 21:45 partition4
    drwxr-xr-x.  3 root root 4096 Feb  9 16:08 sirr
    [root@controller-2 ~]# 

The backup files for each partition are stored in the **/var/F5/partition<ID>/configs** directory. You will need to copy off each chassis partitions backup file. You can use SCP to do this from the shell.

.. code-block:: bash

    [root@controller-2 ~]# ls -al /var/F5/partition4/configs
    total 52
    drwxrwxr-x.  2 root admin    43 Mar 20 06:10 .
    drwxr-xr-x. 22 root root   4096 Mar 10 21:45 ..
    -rw-r--r--.  1 root root  46954 Mar 20 06:10 3-20-2021-bigpartition-backup
    [root@controller-2 ~]# 

Below is an example using SCP to copy off the backup file from partition ID 4, you should do this for each of the partitions:

.. code-block:: bash

    [root@controller-2 ~]# scp /var/F5/partition4/configs/3-20-2021-bigpartition-backup root@10.255.0.142:/var/www/server/1/.
    root@10.255.0.142's password: 
    3-20-2021-bigpartition-backup                                                             100%   46KB  23.7MB/s   00:00    
    [root@controller-2 ~]# 
    
Now repeat the same steps for chassis partition smallpartition. 

