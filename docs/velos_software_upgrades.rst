=================
Software Upgrades
=================

F5OS-C System Controller Upgrades
=================================

The system controllers are fully redundant, however during software upgrades there can be outages of the entire chassis with the initial 1.1.x releases of F5OS. v1.2.x versions of F5OS-C have introduced a rolling upgrade capability for the system controller upgrade process, which minimizes disruption to the chassis. The chassis must be already running a version of F5OS 1.2.x or later to take advantage of this capability. Upgrades from 1.1.x versions to a 1.2.x version will not see rolling upgrade functionality.

This means that both system controllers will be updated at the same time thus causing an outage for all services within that chassis when running v1.1.x F5OS versions. For this reason, it is recommended you upgrade the system controllers during outage window and failover all services to the other chassis that is paired with the one you’re upgrading. For 1.2.x and later upgrades of F5OS-C on the system controllers, a rolling upgrade occurs where the standby controller is upgraded first, and when completed it will go to an acitve state, and the remaining controller will be upgraded.

When upgrading the system controllers, you will have a choice of upgrading either a bundled release, meaning **OS** and **Services** are **bundled** together in an ISO image, or **unbundled** where you can upgrade service and/or OS independently. F5 recommends using the bundled/ISO option for upgrades at this time. In the future, unbundled options may be utilized for some upgrades.

.. image:: images/velos_software_upgrades/image1.png
  :align: center
  :scale: 70%

.. image:: images/velos_software_upgrades/image2.png
  :align: center
  :scale: 70%

The outage for a service-only upgrade on the system controller is smaller than if the entire OS was upgraded. A service upgrade will not cause a reboot of the controllers, where an OS upgrade will. If the OS does not need to be upgraded to address an issue, then you can choose to only upgrade the services. The OS is more of the container/OS layer, where most of the F5 specific code is in the service layer. Most upgrades currently require both service and OS to be upgraded, but in the future this service versus OS upgrades may become available. Upgrades are easily accomplished via the system controller UI. First you would upload F5OS images for both the controllers as well as the partitions into the system controllers. 

Uploading Controller and Partition Images via the webUI
-----------------------------------------------------

You can do this from the **Software Management -> Partitions Image** and **Controller Images** pages.

.. image:: images/velos_software_upgrades/image3.png
  :align: center
  :scale: 70%

.. image:: images/velos_software_upgrades/image4.png
  :align: center
  :scale: 70%

Click **Add** and you will be prompted to provide the configuration details for a remote HTTPS server where the images can be downloaded from. You will need to have a remote HTTPS server that allows downloads in order to import images into the system via the webUI. You can alternatively use the **Upload** option to upload an F5OS-C system controller or chassis partition ISO file from a client machine through the browser.

You can also upload or import F5OS-C images for both the system controllers and the chassis partitions using the **System Settings > File Utilities** page.

.. image:: images/velos_software_upgrades/image5.png
  :align: center
  :scale: 70%

Alternatively, you may also upload images to the controller through the **System Settings -> File Utilities** page. You can select the **images/staging** option from the drop-down menu to import new controller or partition images. Once uploaded into the staging area they will be imported and made available for upgrades after a brief delay.

.. image:: images/velos_software_upgrades/image6.png
  :align: center
  :scale: 70%

After the upload completes, it will take some time for it to be replicated to the standby system controller. At that point it should show up in the CLI and webUI. If you don’t see it immediately, be patient and wait a few minutes for it to show up, as it will not appear until the internal replication is completed.

Uploading Controller and Partition Images via the CLI
-----------------------------------------------------

The current implementation of **file import** in the CLI relies on either a remote HTTPS, SCP, or SFTP server hosting the image files to be imported. The files should be imported into the **images/staging** directory. Once the file import is initiated, you can check its status using the **file transfer-status** command.


.. code-block:: bash

    syscon-2-active# file import remote-host 10.255.0.142 remote-file /upload/F5OS-C-1.2.1-10781.CONTROLLER.iso local-file images/staging/F5OS-C-1.2.1-10781.CONTROLLER.iso username corpuser insecure 

    syscon-2-active# file transfer-status                                                                                                                                                             
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            |Time                
    1    |Import file|HTTPS   |images/staging/F5OS-C-1.2.1-10781.CONTROLLER.iso            |10.255.0.142        |/upload/F5OS-C-1.2.1-10781.CONTROLLER.iso                   |In Progress (5.0%)|Thu Sep 16 17:11:51 2021
    2    |Import file|HTTPS   |images/import/iso/F5OS-C-1.2.1-10781.CONTROLLER.iso         |10.255.0.142        |uploads/F5OS-C-1.2.1-10781.CONTROLLER.iso                   |File Not Found, HTTP Error 404|Thu Sep 16 16:18:27 2021

You can alternatively copy the controller and partition images into the floating IP address of the system controllers. You would use the **root** account and the target directory should be **/var/import/staging/**.

.. code-block:: bash

    root@xubuntu-vm# scp F5OS-C-1.2.1-10781.CONTROLLER.iso root@10.255.0.147:/var/import/staging/.

    root@xubuntu-vm# scp F5OS-C-1.2.1-10781.PARTITION.iso root@10.255.0.147:/var/import/staging/.


Uploading Controller and Partition Images via the API
-----------------------------------------------------

As with the webUI, the current implementation of **file import** in the API relies on either a remote HTTPS, SCP, or SFTP server hosting the image files to be imported. The files should be imported into the **images/staging** directory. Once the file import is initiated you can check its status using the **file transfer-status** API calls.

List the current system controller and partitions images in the images/staging directory via API calls:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/list

The following JSON should be in the payload of the API call above.

.. code-block:: json

    {
    "f5-utils-file-transfer:path": "images/staging"
    }

Below is an example output:

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "entries": [
                {
                    "name": "\nBIGIP-14.1.4.2-0.0.5.ALL-VELOS.qcow2.zip.bundle\nF5OS-C-1.1.2-6101.PARTITION.iso\nF5OS-C-1.2.0-10357.CONTROLLER.iso\nF5OS-C-1.2.0-10357.PARTITION.iso\nF5OS-C-1.2.0-6345.PARTITION.DEV.iso\nF5OS-C-1.2.0-6433.CONTROLLER.DEV.iso\nF5OS-C-1.2.0-6435.CONTROLLER.DEV.iso\nF5OS-C-1.2.0-7091.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-7091.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.0-7451.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-7451.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.0-8813.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-9003.PARTITION.iso\nF5OS-C-1.2.0-9496.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-9496.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10692.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.1-10692.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10781.PARTITION.iso\ncontroller.1.1.2-6101.iso"
                }
            ]
        }
    }

You can then query the **images/import** directory for various image types like ISO, Service, or OS:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/list

.. code-block:: json

    {
    "f5-utils-file-transfer:path": "images/import/iso"
    }

Below is an example output:

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "entries": [
                {
                    "name": "\nBIGIP-14.1.4.2-0.0.5.ALL-VELOS.qcow2.zip.bundle\nF5OS-C-1.1.2-6101.PARTITION.iso\nF5OS-C-1.2.0-10357.CONTROLLER.iso\nF5OS-C-1.2.0-10357.PARTITION.iso\nF5OS-C-1.2.0-6345.PARTITION.DEV.iso\nF5OS-C-1.2.0-6433.CONTROLLER.DEV.iso\nF5OS-C-1.2.0-6435.CONTROLLER.DEV.iso\nF5OS-C-1.2.0-7091.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-7091.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.0-7451.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-7451.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.0-8813.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-9003.PARTITION.iso\nF5OS-C-1.2.0-9496.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.0-9496.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10692.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.1-10692.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10781.PARTITION.iso\ncontroller.1.1.2-6101.iso"
                }
            ]
        }
    }

To import images, use the following API examples for controller and partition images. To import the **controller** image:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/api/data/f5-utils-file-transfer:file/import

.. code-block:: json

    {
        "input": [
            {
                "remote-host": "10.255.0.142",
                "remote-file": "/upload/{{Controller_ISO_Image_Full}}",
                "local-file": "images/staging/",
                "insecure": "",
                "f5-utils-file-transfer:username": "corpuser",
                "f5-utils-file-transfer:password": "Passw0rd!!"
            }
        ]
    }

To import the **partition** image:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/api/data/f5-utils-file-transfer:file/import

.. code-block:: json

    {
        "input": [
            {
                "remote-host": "10.255.0.142",
                "remote-file": "/upload/{{Partition_ISO_Image_Full}}",
                "local-file": "images/staging/",
                "insecure": "",
                "f5-utils-file-transfer:username": "corpuser",
                "f5-utils-file-transfer:password": "password"
            }
        ]
    }

You can then check on the file transfer status with the following API call:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/transfer-status

A response like the one below will provide the status of the transfer:

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "result": "\nS.No.|Operation  |Protocol|Local File Path  |Remote Host |Remote File Path                                        |Status            |Time                \n1    |
    Import file|HTTPS   |images/staging/F5OS-C-1.2.1-10781.PARTITION.iso |10.255.0.142 |/upload/F5OS-C-1.2.1-10781.PARTITION.iso                    |File Not Found, HTTP Error 404|Thu Sep 16 20:27:23 2021\n2    
    |Import file|HTTPS   |images/staging/F5OS-C-1.2.1-10781.CONTROLLER.iso            |10.255.0.142        |F5OS-C-1.2.1-10781.CONTROLLER.iso                           |File Not Found, HTTP Error 404|Thu Sep 16 20:19:56 2021\n3    
    |Import file|HTTPS   |images/import/iso/F5OS-C-1.2.1-10781.CONTROLLER.iso         |10.255.0.142        |uploads/F5OS-C-1.2.1-10781.CONTROLLER.iso                   |File Not Found, HTTP Error 404|Thu Sep 16 16:18:27 2021\n4    
    |Import file|HTTPS   |images/staging/F5OS-C-1.2.1-10781.CONTROLLER.iso            |10.255.0.142        |/upload/F5OS-C-1.2.1-10781.CONTROLLER.iso                   |         Completed|Thu Sep 16 20:24:26 2021\n"
        }
    }

After transferring the file you can view the contents of the images/staging directory. The file will then go through an import process before it is ready for use.

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/list

.. code-block:: json

    {
    "f5-utils-file-transfer:path": "images/staging"
    }

You will see all the files in the images/staging directory:

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "entries": [
                {
                    "name": "\nF5OS-C-1.2.0-10357.CONTROLLER.iso\nF5OS-C-1.2.0-10357.PARTITION.iso\nF5OS-C-1.2.1-10692.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.1-10692.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10781.CONTROLLER.iso\ncontroller.1.1.2-6101.iso"
                }
            ]
        }
    }

You can then monitor the images/import/iso directory to see when the file is ready to use for upgrade:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/list

.. code-block:: json

    {
    "f5-utils-file-transfer:path": "images/import/iso"
    }

You’ll see output similar to the example below. Once the file shows up here you are ready to upgrade.

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "entries": [
                {
                    "name": "\nF5OS-C-1.2.0-10357.CONTROLLER.iso\nF5OS-C-1.2.0-10357.PARTITION.iso\nF5OS-C-1.2.1-10692.CONTROLLER.CANDIDATE.iso\nF5OS-C-1.2.1-10692.PARTITION.CANDIDATE.iso\nF5OS-C-1.2.1-10781.CONTROLLER.iso\ncontroller.1.1.2-6101.iso"
                }
            ]
        }
    }

Upgrading the System Controllers via webUI
------------------------------------------

Once the new images are loaded, you can perform the upgrade from the **System Settings > Controller Management** screen. Currently it is recommended you use the **Bundled** option to upgrade using the ISO. In the future, there may be cases where **Unbundled** (separate OS or Service upgrades) are recommended. Once you click **Save** the upgrade process will begin. For F5OS versions 1.1.x there is no rolling upgrade support and both controllers will reboot immediately taking the entire chassis offline. For F5OSv1.2 rolling upgrade support has been added, but you must be on a v1.2.x release or later to take advantage of this new functionality. With rolling upgrade support traffic disruption should be minimal during the upgrade process.

.. image:: images/velos_software_upgrades/image7.png
  :align: center
  :scale: 70%


Upgrading the System Controllers via CLI
----------------------------------------

In the system controller CLI you can use the **show image** command to see the currently installed software versions for each system controller.

.. code-block:: bash

    syscon-1-active# show image
    VERSION OS                                   IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   1           ready               false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  true   

    VERSION                                             
    SERVICE                                      IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.0-6101   1           ready   2021-05-09  false  
    1.1.2-6101   1           ready   2021-05-09  false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  true   

    VERSION ISO                                  IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   1           ready   2021-05-09  false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  false  

    VERSION OS                                   IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  true   

    VERSION                                             
    SERVICE                                      IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.0-6101   2           ready   2021-05-09  false  
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  true   

    VERSION ISO                                  IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  false  

    VERSION OS                                   IN               
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME  ID  
    --------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false            
    1.2.1-10692  1           ready   2021-08-30  false            

    VERSION                                                          
    SERVICE                                      IN                  
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME     ID  
    -----------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false               
    1.2.1-10692  1           ready   2021-08-30  true   default  1   

    VERSION ISO                                  IN                         
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME            ID  
    ------------------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false                      
    1.2.1-10692  1           ready   2021-08-30  true   Production    2   
                                                        default         1   
                                                        smallpartition  3   

    VERSION OS                                   IN               
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME  ID  
    --------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false            
    1.2.1-10692  2           ready   2021-08-30  false            

    VERSION                                                          
    SERVICE                                      IN                  
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME     ID  
    -----------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false               
    1.2.1-10692  2           ready   2021-08-30  true   default  1   

    VERSION ISO                                  IN                         
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME            ID  
    ------------------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false                      
    1.2.1-10692  2           ready   2021-08-30  true   Production    2   
                                                        default         1   
                                                        smallpartition  3   

    syscon-1-active# 

The command show running-config image will show the current configuration for software images. You can enter config mode and change the configuration using the system image set-version command and then commit to initiate an upgrade.

.. code-block:: bash

    syscon-1-active# show running-config image 
    image controller config os os 1.1.2-6101
    !
    image controller config os os 1.2.0-10357
    !
    image controller config os os 1.2.1-10692
    !
    image controller config os os 1.2.1-10781
    !
    image controller config services service 1.1.0-6101
    !
    image controller config services service 1.1.2-6101
    !
    image controller config services service 1.2.0-10357
    !
    image controller config services service 1.2.1-10692
    !
    image controller config services service 1.2.1-10781
    !
    image controller config iso iso 1.1.2-6101
    service 1.1.2-6101
    os      1.1.2-6101
    !
    image controller config iso iso 1.2.0-10357
    service 1.2.0-10357
    os      1.2.0-10357
    !
    image controller config iso iso 1.2.1-10692
    service 1.2.1-10692
    os      1.2.1-10692
    !
    image controller config iso iso 1.2.1-10781
    service 1.2.1-10781
    os      1.2.1-10781
    !
    image partition config os os 1.2.0-10357
    !
    image partition config os os 1.2.1-10692
    !
    image partition config services service 1.2.0-10357
    !
    image partition config services service 1.2.1-10692
    !
    image partition config iso iso 1.2.0-10357
    service 1.2.0-10357
    os      1.2.0-10357
    !
    image partition config iso iso 1.2.1-10692
    service 1.2.1-10692
    os      1.2.1-10692
    !
    syscon-1-active# 

.. code-block:: bash

    syscon-1-active(config)# system image set-version iso-version 1.2.1-10781 
    response Controller iso version has been set
    syscon-1-active(config)# 


An upgrade of the system controllers should automatically start after the above command is entered. You can follow the upgrade progress by issuing the command **show system image**:

.. code-block:: bash

    syscon-2-active# show system image 
                        SERVICE      ISO      INSTALL      
    NUMBER  OS VERSION   VERSION      VERSION  STATUS       
    --------------------------------------------------------
    1       1.2.1-10692  1.2.1-10692  -        in-progress  
    2       1.2.1-10692  1.2.1-10692  -        pending      

    syscon-2-active# 


Upgrading the System Controllers via API
----------------------------------------

To upgrade the system controllers via the API you must first run the check version API call with the version you want to update to:

.. code-block:: bash

 POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-controller-image:image/f5-system-controller-image:check-version

.. code-block:: json

    {
        "f5-system-controller-image:iso-version": "{{Controller_ISO_Image_Full}}"
    }

If the compatability check passes then you will get a message like the one below, and it is safe to install the new image via the set-version API call:

.. code-block:: json

    {
        "f5-system-controller-image:output": {
            "response": "Compatibility verification succeeded."
        }
    }

This is the Set Version API call that will initiate the upgrade:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-controller-image:image/f5-system-controller-image:set-version

.. code-block:: json

    {
        "f5-system-controller-image:iso-version": "{{Controller_ISO_Image_Full}}"
    }

If the upgrade is successful, you will get notification like the message below:

.. code-block:: json

    {
        "f5-system-controller-image:output": {
            "response": "Controller iso version has been set"
        }
    }



Chassis Partition Upgrades
==========================

Upgrading a Chassis Partition via the webUI
-----------------------------------------

Upgrade of chassis partitions is performed from the system controller webUI **Partition Management** screen. You must first select the checkmark next to the chassis partition you wish to upgrade and then click **Edit**. You’ll now be able perform either a **bundled** or **unbundled** software upgrade of the chassis partition. At this time a bundled upgrade using the ISO image is recommended. Once you click **Save**, the partition will be brought offline and back online after the upgrade. All tenants will be suspended during this time so an outage will occur for this chassis partition only. 

.. image:: images/velos_software_upgrades/image8.png
  :align: center
  :scale: 70%


Upgrading a Chassis Partition via the CLI
-----------------------------------------

In the system controller CLI you can use the **show image** command to see the currently installed software versions for each chassis partition.

.. code-block:: bash

    syscon-1-active# show image
    VERSION OS                                   IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   1           ready               false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  true   

    VERSION                                             
    SERVICE                                      IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.0-6101   1           ready   2021-05-09  false  
    1.1.2-6101   1           ready   2021-05-09  false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  true   

    VERSION ISO                                  IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   1           ready   2021-05-09  false  
    1.2.0-10357  1           ready   2021-08-21  false  
    1.2.1-10692  1           ready   2021-08-30  false  
    1.2.1-10781  1           ready   2021-09-01  false  

    VERSION OS                                   IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  true   

    VERSION                                             
    SERVICE                                      IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.0-6101   2           ready   2021-05-09  false  
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  true   

    VERSION ISO                                  IN     
    CONTROLLER   CONTROLLER  STATUS  DATE        USE    
    ----------------------------------------------------
    1.1.2-6101   2           ready   2021-05-09  false  
    1.2.0-10357  2           ready   2021-08-21  false  
    1.2.1-10692  2           ready   2021-08-30  false  
    1.2.1-10781  2           ready   2021-09-01  false  

    VERSION OS                                   IN               
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME  ID  
    --------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false            
    1.2.1-10692  1           ready   2021-08-30  false            

    VERSION                                                          
    SERVICE                                      IN                  
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME     ID  
    -----------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false               
    1.2.1-10692  1           ready   2021-08-30  true   default  1   

    VERSION ISO                                  IN                         
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME            ID  
    ------------------------------------------------------------------------
    1.2.0-10357  1           ready   2021-08-21  false                      
    1.2.1-10692  1           ready   2021-08-30  true   Production    2   
                                                        default         1   
                                                        smallpartition  3   

    VERSION OS                                   IN               
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME  ID  
    --------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false            
    1.2.1-10692  2           ready   2021-08-30  false            

    VERSION                                                          
    SERVICE                                      IN                  
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME     ID  
    -----------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false               
    1.2.1-10692  2           ready   2021-08-30  true   default  1   

    VERSION ISO                                  IN                         
    PARTITION    CONTROLLER  STATUS  DATE        USE    NAME            ID  
    ------------------------------------------------------------------------
    1.2.0-10357  2           ready   2021-08-21  false                      
    1.2.1-10692  2           ready   2021-08-30  true   Production    2   
                                                        default         1   
                                                        smallpartition  3   

    syscon-1-active# 

The command **show running-config image** will show the current configuration for software images. You can enter **config** mode and change the configuration using the **system image set-version** command and then **commit** to initiate an upgrade.


Upgrading a Chassis Partition via the API
-----------------------------------------

To upgrade a chassis partition via the API you must first run the check version API call with the version you want to update to:

.. code-block:: bash

 POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-system-partition:partitions/partition=Production/check-version

.. code-block:: json

    {
        "input": {
            "iso-version": "{{Partition_ISO_Image}}"
        }
    }

If the compatability check passes then you will get a message like the one below, and it is safe to install the new image via the set-version API call:

.. code-block:: json

    {
        "f5-system-partition:output": {
            "result": "Partition upgrade compatibility check succeeded."
        }
    }

This is the Set Version API call that will initiate the upgrade:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-system-partition:partitions/partition=Production/set-version

.. code-block:: json

    {
        "input": {
            "iso-version": "{{Partition_ISO_Image}}"
        }
    }

If the upgrade is successful, you will get notification like the message below:

.. code-block:: json

    {
        "f5-system-partition:output": {
            "result": "Version update successful."
        }
    }



Tenant Images and Upgrades
==========================

-------------------------------------
Loading Tenant Images for New Tenants
-------------------------------------

Tenant software images are loaded directly into each chassis partition. If you have more than one chassis partition, you’ll need to load tenant images for each one independently. The first release of VELOS only supported TMOS v14.1.4, and 15.1.4 support has been added with the release of F5OS v1.2.1. No other TMOS versions are supported other than hotfixes or rollups based on those versions of software. 

Before deploying any tenant, you must ensure you have a proper tenant software release loaded into the chassis partition. Under **Tenant Management** there is a page for uploading tenant software images. There are TMOS images specifically for VELOS. Only supported VELOS TMOS releases should be loaded into this system. Do not attempt to load older or even newer images unless there are officially supported on VELOS. 

There is an option to **Add** new releases which will open a pop-up window that will ask for remote host, path and optional authentication parameters. You may only upload from a remote HTTPS server using the webUI in the current VELOS release. The **Tenant Images** page will also indicate of an image is in use by a tenant, and if it is replicated to other blades in the chassis partition.

.. image:: images/velos_software_upgrades/image9.png
  :align: center
  :scale: 70%


.. image:: images/velos_software_upgrades/image10.png
  :align: center
  :scale: 70%

If an HTTPS server is not available, you may upload a tenant image using SCP directly to the chassis partition. Simply scp an image to the chassis partitions out-of-band management IP address using the admin account and a path of **IMAGES**. 

.. code-block:: bash

    scp BIGIP-bigip14.1.x-tmos-bugfix-14.1.3.1-0.0.586.ALL-VELOS.qcow2.zip.bundle admin@10.255.0.148:IMAGES

You may also import the tenant image file from the chassis partition CLI. Use the file import command to get the tenant image file from a remote HTTPS server. You must do this for each chassis partition:

.. code-block:: bash

    Production# file import remote-host 10.255.0.142 remote-file /upload/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle local-file images/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle username corpuser insecure

Repeat for other chassis partitions:

.. code-block:: bash

    smallpartition# file import remote-host 10.255.0.142 remote-file /upload/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle local-file images/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle username corpuser insecure


The command **file transfer-status** will provide details of the transfer progress and any errors:

.. code-block:: bash

    Production-1# file import remote-host 10.255.0.142 remote-file /upload/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle local-file images/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle username corpuser insecure
    Value for 'password' (<string>): ********
    result File transfer is initiated.(images/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle)



    Production-1# file transfer-status 
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            |Time                
    1    |Import file|HTTPS   |images/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle       |10.255.0.142        |/upload/BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle      |In Progress (15.0%)|Fri Sep 17 15:36:29 2021


You can view the current tenant images and their status in the chassis partition CLI:

.. code-block:: bash

    Production-1# show images
                                                                            IN                 
    NAME                                                                       USE    STATUS      
    ----------------------------------------------------------------------------------------------
    BIGIP-14.1.3-0.0.7.ALL-VELOS.qcow2.zip.bundle                              false  replicated  
    BIGIP-14.1.4-0.0.619.ALL-VELOS.qcow2.zip.bundle                            true   replicated  
    BIGIP-bigip14.1.x-tmos-bugfix-14.1.3.1-0.0.586.ALL-VELOS.qcow2.zip.bundle  false  replicated

To copy a tenant image into a chassis partition, use the following API call to the chassis partition IP address:

.. code-block:: bash

    POST https://{{velos_chassis1_chassis_partition1_ip}}:8888/api/data/f5-utils-file-transfer:file/import

.. code-block:: json

    {
        "input": [
            {
                "remote-host": "10.255.0.142",
                "remote-file": "upload/{{Tenant_Image}}",
                "local-file": "images/{{Tenant_Image}}",
                "insecure": "",
                "f5-utils-file-transfer:username": "corpuser",
                "f5-utils-file-transfer:password": "Passw0rd!!"
            }
        ]
    }

To list the current tenant images available on the chassis partition use the following API Call:

.. code-block:: bash

    GET https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/f5-tenant-images:images

Below is output generated from the previous command:

.. code-block:: json

    {
        "f5-tenant-images:images": {
            "image": [
                {
                    "name": "BIGIP-15.1.4-0.0.46.ALL-VELOS.qcow2.zip.bundle",
                    "in-use": true,
                    "status": "replicated"
                },
                {
                    "name": "BIGIP-15.1.4-0.0.47.ALL-VELOS.qcow2.zip.bundle",
                    "in-use": false,
                    "status": "replicated"
                }
            ]
        }
    }

---------------
Tenant Upgrades
---------------

Tenants are upgraded via the normal TMOS upgrade process. Find the proper ISO image and ensure it is of a supported VELOS release, and upload it into the TMOS tenant. Once uploaded you can upgrade and boot into the new version. Currently VELOS does not allow an upgrade of the tenant from inside the F5OS layer, you must perform the upgrade from inside the tenant.

**NOTE: Currently VELOS does not provide a shared image repository for all tenants to upgrade from. With vCMP guests, VIPRION allowed for an image to be loaded once into the host layer, and all tenants had access to that repository to use to upgrade.**

