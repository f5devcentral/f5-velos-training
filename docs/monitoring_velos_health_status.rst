=====================================
Montoring VELOS Health & Alert Status
=====================================

VELOS has many components and subsystems which can be monitored via CLI, GUI, API, and SNMP. It may be difficult to sift through all the end points to determine which are the key ones that can quicky provide health of the chassis. This section will provide guidance on how to quickly get health and alert status of the VLEOS system, while other sections will focus on getting deeper detail.

Active Alerts
=============

The VELOS system has an alerting system where certain known issues will raise alerts when the occur, and will be be cleared when they are addressed or self-heal.

------------------------------
Checking Active Alerts via CLI
------------------------------

------------------------------
Checking Active Alerts via GUI
------------------------------

In the system controller GUI you can go to the **System Events > Alarms & Events** page to see if there are any known alerts for the system. The alerting page is focused on **Active** alerts, and not issues that have cleared. If for example the temperature rises beyond an acceptable threshold then a temperature alert will be raised. It will be seen in this page. If the temperature then falls back into a safe range then the alert will be removed. 

.. image:: images/monitoring_velos_health_status/image1.png
  :align: center
  :scale: 70%

------------------------------
Checking Active Alerts via API
------------------------------

Recent system level alerts can be accessed via the API. Below is an API call to see all the system events that have cocured for a particular chassis:

.. code-block:: bash

    GET https://{{System-Controller-IP}}:8888/restconf/data/openconfig-system:system/f5-event-log:events



.. code-block:: json

    {
        "f5-event-log:events": {
            "event": [
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-05-24 15:53:18.820164630 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-05-24 15:53:19.967579072 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-05-24 15:53:19.978854195 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-05-24 15:53:21.049898161 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-05-24 15:53:26.015915252 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-05-24 15:53:27.966554138 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-05-24 15:53:28.856929312 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-24 15:53:36.609780988 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-24 15:53:40.490642113 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-05-24 15:53:40.497334043 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-05-24 15:53:26.746574994 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-05-24 15:53:40.509161929 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-05-24 15:53:28.706752103 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-05-24 15:53:28.715491054 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-05-24 15:53:30.717324742 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-05-24 15:53:30.855158278 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-05-24 15:53:30.866478756 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-05-24 15:53:32.702577376 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-05-24 15:53:34.604942495 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-05-24 15:53:42.172936771 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-24 15:53:44.108120781 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-05-24 15:53:44.763209737 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update is running for sirr \" \"2021-05-24 15:53:50.355583264 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update completed for sirr \" \"2021-05-24 15:53:54.860605432 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-05-24 15:53:54.959361073 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update is running for sirr \" \"2021-05-24 15:53:55.977932249 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update completed for sirr \" \"2021-05-24 15:53:56.004017282 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-05-24 19:02:25.967760976 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-05-24 19:02:25.967814413 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-05-24 19:02:25.984620974 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-05-24 19:02:25.984658200 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-05-24 19:02:26.005916081 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-05-24 19:02:26.005953590 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-05-24 19:27:26.907130040 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-05-24 19:27:32.909166793 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-24 19:27:32.918458222 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-24 19:27:33.225942722 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-24 19:27:40.575885860 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-24 19:27:40.699221495 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:27:45.296321858 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:27:45.351543047 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:27:46.473379078 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:27:46.758451350 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-05-24 19:28:15.154986705 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-05-24 19:28:15.426611864 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-05-24 19:28:16.693070219 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-05-24 19:28:16.929074328 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-05-24 19:28:59.482959232 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-05-24 19:29:05.495334551 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-24 19:29:05.501245699 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-24 19:29:13.225536688 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:29:19.095000767 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-24 19:29:26.520634735 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-fault EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-05-24 19:29:49.115375341 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-fault EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-05-24 19:29:56.854690134 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-05-26 02:41:33.773124875 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-05-26 02:41:35.695101564 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-05-26 02:41:35.699045726 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-05-26 02:41:39.786044205 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-05-26 02:41:39.794249414 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-05-26 02:41:39.801413197 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-05-26 02:41:40.461757212 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-05-26 02:42:07.768703623 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-05-26 02:42:07.773478111 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-05-26 02:42:07.777640774 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-05-26 02:42:07.784882772 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-05-26 02:42:07.797839796 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-05-26 02:41:34.187592931 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-05-26 02:41:35.217855071 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-05-26 02:41:35.225443025 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-05-26 02:41:35.229464078 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-05-26 02:41:43.919541915 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-05-26 02:42:05.250170202 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-05-26 02:42:05.318741777 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-05-26 02:42:06.203445372 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-05-26 02:42:07.211962644 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-05-26 02:42:07.802468848 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-05-26 02:42:07.812836190 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-05-26 02:42:07.837459150 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-05-26 02:42:10.436404999 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-05-26 02:42:10.822523042 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-26 04:06:34.843286399 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-26 04:06:36.864531942 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-05-26 04:06:36.864577488 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-05-26 04:06:39.864693543 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-26 04:06:40.767393044 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-05-26 04:06:44.080096986 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-26 04:06:46.861342298 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-05-26 04:06:46.861384173 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-05-26 04:06:47.864891691 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-26 04:06:47.868305537 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:06:50.071892384 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-05-26 04:06:52.078973703 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-05-26 04:06:52.083057955 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-05-26 04:06:52.864912359 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-05-26 04:06:52.864961319 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-26 04:06:54.614997814 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-05-26 04:06:59.255493894 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:07:17.020835533 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:07:17.088817305 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has FEC corrected errors, value=0\" \"2021-05-26 04:07:17.113025955 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:07:17.117741108 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:07:25.900036452 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value={fecUncorrected}\" \"2021-05-26 04:07:25.983571289 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-05-26 04:07:25.991771422 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-05-26 04:07:46.995681057 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no FEC corrected errors\" \"2021-05-26 04:07:47.082891427 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-05-26 04:07:47.098664549 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-05-26 04:07:47.113937967 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-05-26 04:07:55.959708798 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-05-26 04:07:56.033014139 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 16:47:50.483963322 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-08 16:47:50.872690000 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-08 16:47:50.885960413 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-08 16:47:51.773515624 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 16:47:54.846390423 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 16:47:54.869295802 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-08 16:47:55.766512589 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-08 16:47:55.771791600 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 16:53:50.381816481 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-08 16:53:50.844177881 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-08 16:53:50.858747580 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-08 16:53:51.773661729 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 16:53:54.845747885 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 16:53:54.854649081 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-08 16:53:55.766681831 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-08 16:53:55.771458661 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-08 16:54:33.726598842 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-08 16:54:34.845042248 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 16:54:35.258054440 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-08 16:54:35.262306870 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 16:54:40.873525623 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 16:54:40.879258523 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-08 16:54:41.263088018 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-08 16:54:41.275300189 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 17:03:50.420107725 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-08 17:03:50.424761234 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-08 17:03:54.412591069 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update is running for lop app\" \"2021-06-08 17:03:54.420350572 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 17:03:54.447747574 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 17:03:54.451895777 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-08 17:03:54.456023628 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update completed for lop app\" \"2021-06-08 17:04:58.086195596 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-08 17:04:58.185480614 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 17:04:58.296640449 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-08 17:04:58.306625393 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-06-08 17:04:58.314679704 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-08 17:05:08.315560344 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 17:05:29.138154282 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 17:05:29.142686430 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-08 17:05:29.146823745 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc1 present\" \"2021-06-08 17:05:29.494118744 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc2 present\" \"2021-06-08 17:05:29.512884205 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Lcd present\" \"2021-06-08 17:05:29.532670877 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-08 17:05:29.643718762 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-08 17:05:29.675363738 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-08 17:05:29.698398473 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-08 17:05:29.711417818 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 17:05:29.762969436 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 17:05:29.778736048 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-08 17:05:29.825566300 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc1 present\" \"2021-06-08 17:05:29.831658448 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc2 present\" \"2021-06-08 17:05:29.839974584 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Lcd present\" \"2021-06-08 17:05:29.846969700 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-08 17:05:29.883731813 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-08 17:05:29.894288039 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-08 17:05:29.912027778 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-08 17:05:29.924891471 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 17:05:29.931330746 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 17:05:29.938910072 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-08 17:05:29.959258934 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc1 present\" \"2021-06-08 17:05:29.980776014 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc2 present\" \"2021-06-08 17:05:29.993961274 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Lcd present\" \"2021-06-08 17:05:30.010096538 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-08 17:05:30.022915999 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-08 17:05:30.036778056 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-08 17:05:30.049579785 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-08 17:05:30.068270819 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-08 17:05:30.089275176 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-08 17:05:30.153062298 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-08 17:05:30.163256137 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc1 present\" \"2021-06-08 17:05:30.170689627 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc2 present\" \"2021-06-08 17:05:30.186959979 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Lcd present\" \"2021-06-08 17:05:30.415730394 UTC\""
                },
                {
                    "log": "65550 fan-controller-1 firmware-update-status EVENT NA \"Firmware update is running for fanCtrl1 vfc app\" \"2021-06-08 17:05:44.412068547 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller power-good\" \"2021-06-08 17:06:36.413020951 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:36.418501819 UTC\""
                },
                {
                    "log": "65550 fan-controller-1 firmware-update-status EVENT NA \"Firmware update completed for fanCtrl1 vfc app\" \"2021-06-08 17:06:36.423887571 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault CLEAR NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:06:40.417763793 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:40.417801090 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller power-good\" \"2021-06-08 17:06:40.428203684 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller power-good\" \"2021-06-08 17:06:51.540839226 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:06:52.413613591 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:52.413638350 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault CLEAR NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:06:58.413341798 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:58.413373514 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:06:58.420307077 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:58.420328493 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:58.424615228 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:06:58.430414384 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault CLEAR NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:00.414578044 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:00.414612997 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller power-good\" \"2021-06-08 17:07:00.429142189 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller power-good\" \"2021-06-08 17:07:12.414632674 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:12.423598987 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:12.423621530 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault CLEAR NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:15.899274567 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:15.899298140 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:16.413352437 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:16.413373587 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault CLEAR NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:18.416522469 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:18.416565500 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault ASSERT NOTICE \"Power fault detected in hardware\" \"2021-06-08 17:07:18.425185651 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:18.425206889 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:18.434689401 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:07:18.451955459 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Asserted: exhaust hot swap controller fault\" \"2021-06-08 17:12:27.157634877 UTC\""
                },
                {
                    "log": "65545 fan-controller-1 power-fault EVENT NA \"Deasserted: exhaust hot swap controller power-good\" \"2021-06-08 17:12:31.086107841 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update is running for lop app\" \"2021-06-08 17:13:00.986080031 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-08 17:14:14.809922752 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: PSU Controller 1 Present\" \"2021-06-08 17:14:05.648513109 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: PSU Controller 2 Present\" \"2021-06-08 17:14:05.652957568 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: LCD Present\" \"2021-06-08 17:14:05.656740151 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: local arbitration health state\" \"2021-06-08 17:14:13.647324752 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-08 22:11:29.281803111 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-08 22:11:29.677303435 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-08 22:11:35.268799257 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-08 22:11:37.279560573 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-08 22:11:37.282989678 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-08 22:11:37.674657077 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-08 22:11:37.678017062 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-08 22:11:43.266457169 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-08 22:11:43.284048903 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-08 22:11:43.270078179 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-08 22:11:43.786774792 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-08 22:11:49.387477925 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-06-08 22:12:10.606421095 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has FEC corrected errors, value=0\" \"2021-06-08 22:12:10.660741570 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-06-08 22:12:10.663751822 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-08 22:12:10.709820336 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-08 22:12:46.585336909 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no FEC corrected errors\" \"2021-06-08 22:12:46.595600038 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-06-08 22:12:46.698493855 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-08 22:12:46.710259494 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-09 01:23:00.135970456 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-06-09 01:23:00.144947407 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-09 01:23:00.168975125 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-09 01:23:00.171572929 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-09 01:23:00.173608692 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-06-09 01:23:00.194296345 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-09 01:23:01.284866039 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-06-09 01:23:04.207441597 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-09 01:23:16.411933302 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-09 01:23:39.665604311 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-09 01:23:40.009990588 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-09 01:23:40.022773936 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-09 01:23:40.036592589 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-09 01:23:40.042706112 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-09 01:23:40.048228163 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-09 01:23:40.053041323 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-09 01:23:40.057888924 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-09 01:23:40.064186897 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-09 01:23:40.200183962 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-09 01:23:40.205072619 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-09 01:23:40.218326155 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-09 01:23:40.228089185 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-09 01:23:40.233587027 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-09 01:23:40.235777658 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-06-09 01:23:40.245157028 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-06-09 01:23:40.251466024 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-06-09 01:23:40.257654160 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-06-09 01:23:40.263144121 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-09 01:23:40.265373436 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-09 01:23:40.285950716 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-09 01:23:44.200643398 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-09 01:23:44.209918865 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-09 01:23:44.212017033 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-09 01:23:44.215557304 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-06-09 01:23:44.217768260 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-06-09 01:23:46.221894490 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-06-09 01:23:44.414999532 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-09 01:23:48.700098589 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-09 01:24:16.291993903 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-09 01:24:16.300992654 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-09 01:24:16.307414300 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-09 01:24:16.362208970 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-09 01:24:16.416556797 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-09 01:24:16.615675204 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-09 01:24:16.630965621 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-09 01:24:16.647689570 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-09 01:24:16.661442483 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-09 01:24:16.673100203 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-09 01:24:16.685115395 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-09 01:24:16.696583578 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-09 01:24:16.784133660 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: local arbitration health state\" \"2021-06-09 01:24:08.192552507 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-09 01:24:08.194983897 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-09 01:24:10.192497855 UTC\""
                },
                {
                    "log": "65538 controller-1 unknown-alarm EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-09 01:25:02.192500637 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-09 01:25:02.412339944 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:13:36.036313619 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-06-11 21:13:36.036354552 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:13:36.058165031 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-06-11 21:13:36.058245173 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:13:50.459165274 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-06-11 21:13:50.459323130 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-11 21:19:06.300249022 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-11 21:19:06.345833730 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-11 21:19:14.298699808 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-11 21:19:14.341418977 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-11 21:19:14.302185816 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-11 21:19:14.344871633 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:19:14.420384866 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-11 21:19:14.420425877 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:19:14.435647217 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-11 21:19:14.435694126 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-11 21:19:20.298115028 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-11 21:19:20.340953769 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-11 21:19:52.881882966 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-06-11 21:19:52.941614430 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-11 21:20:12.365281776 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:20:18.414803888 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-11 21:20:18.414842783 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-11 21:20:20.363197478 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-11 21:20:20.366646800 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-11 21:20:20.597347912 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-11 21:20:20.658512157 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-11 21:20:26.374764448 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-06-11 21:20:56.450052658 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-06-11 21:21:26.668642426 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:54:48.436969233 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-06-11 21:54:48.437010280 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-06-11 21:54:48.445763051 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-06-11 21:54:48.445800076 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-11 22:08:27.218070227 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-11 22:08:27.456935094 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-06-11 22:08:32.634402018 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-11 22:08:32.634464321 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-06-11 22:08:34.415576660 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-11 22:08:34.415616457 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-11 22:08:35.215246028 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-11 22:08:35.218631519 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-11 22:08:35.454929310 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-11 22:08:35.458162457 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-11 22:08:41.216182961 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-11 22:08:41.454922097 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-06-11 22:08:46.563046902 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-11 22:08:46.627204201 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-11 22:09:20.496969843 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-11 22:09:20.567874244 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-15 13:38:30.413831632 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-15 13:38:34.412438813 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-06-15 13:38:34.419272332 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Attribute health reset\" \"2021-06-15 13:38:24.303908680 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-15 13:38:32.118920563 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-15 13:38:36.118106953 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-15 13:38:36.126161302 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-15 13:38:36.131559870 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-06-15 13:38:36.137318794 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-06-15 13:39:10.808372894 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-15 13:39:13.836900122 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-15 13:39:13.842365495 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-15 13:39:13.854502992 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-15 13:39:13.865497149 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-15 13:39:14.050079652 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-15 13:39:14.077762379 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-15 13:39:14.162180795 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-15 13:39:49.017914935 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-15 13:46:03.112769628 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-15 13:46:10.120379681 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 15:16:24.927157896 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 15:16:25.073627711 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 15:16:32.860119559 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 15:16:32.863834704 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 15:16:32.923678920 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 15:16:32.929797285 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 15:16:38.874257139 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 15:16:40.923363288 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-15 15:16:59.998124831 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-06-15 15:17:00.021804239 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-06-15 15:17:08.295961098 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-06-15 15:17:08.373446640 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-15 15:17:34.015127299 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-15 15:17:34.082913447 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-06-15 15:17:38.308898782 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-06-15 15:17:38.363647662 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 16:01:04.290909198 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 16:01:12.288749841 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 16:01:12.292175718 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 16:01:18.164485465 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-06-15 16:01:24.161569012 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-06-15 16:01:42.192550948 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-06-15 16:01:54.136683020 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-06-15 16:02:12.290767652 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:23:38.955977480 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:23:40.095187890 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:23:46.092624323 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:23:46.953971834 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:23:46.957634759 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:23:48.093243626 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:23:52.975132261 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:23:53.912319339 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-06-15 20:23:58.348987917 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-06-15 20:23:58.460939067 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-06-15 20:24:00.059590448 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-15 20:24:00.097445994 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-06-15 20:24:28.360753999 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-06-15 20:24:28.411805323 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-15 20:24:30.062004270 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-15 20:24:30.077452401 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:26:12.624891552 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:26:20.622580431 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:26:20.625825811 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:26:23.761909145 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:26:26.567148966 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:26:31.762200324 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:26:31.765500003 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has FEC corrected errors, value=0\" \"2021-06-15 20:26:38.314501213 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=3\" \"2021-06-15 20:26:38.318251169 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:26:38.550428597 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=3\" \"2021-06-15 20:26:40.136105867 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no FEC corrected errors\" \"2021-06-15 20:27:08.339346954 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-06-15 20:27:08.343262197 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=3\" \"2021-06-15 20:27:08.357157163 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=3\" \"2021-06-15 20:27:13.992272932 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-06-15 20:27:14.051641969 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-06-15 20:27:38.261216113 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-15 20:27:44.040692601 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:28:58.718160377 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-06-15 20:29:05.520812935 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:29:06.715659128 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:29:06.719133461 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:29:13.065483708 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-06-15 20:29:13.517679672 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-06-15 20:29:13.521178355 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-06-15 20:29:18.385037541 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-06-15 20:29:19.362347409 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-06-15 20:29:24.079185820 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has FEC corrected errors, value=0\" \"2021-06-15 20:29:24.117729231 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-06-15 20:29:48.371538506 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-06-15 20:29:48.426252392 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no FEC corrected errors\" \"2021-06-15 20:29:54.090015358 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-06-15 20:29:54.121732861 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-06-15 20:30:18.391868147 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-23 18:05:14.825308321 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-23 18:09:39.456776338 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-23 18:09:37.631819600 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-23 18:09:39.553136679 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-06-23 18:16:54.002580191 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-23 18:16:58.024828555 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-23 18:16:58.932111716 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-06-23 18:16:58.936482729 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-06-23 18:16:58.940692170 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-23 18:16:58.944568541 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-06-23 18:16:58.948917156 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-23 18:17:23.996337126 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-23 18:17:25.987003458 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-06-23 18:17:31.999455155 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-06-23 18:17:33.993437368 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-23 18:17:34.005583345 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-23 18:17:35.355188220 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-06-23 18:17:39.028900143 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-06-23 18:17:18.864371454 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-06-23 18:17:18.868626858 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-06-23 18:17:18.872573814 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-06-23 18:17:22.860930934 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-06-23 18:17:24.806441161 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-06-23 18:17:30.851140281 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-06-23 18:18:35.840347237 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 14:43:15.998986036 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-07-19 14:51:15.999816769 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-07-19 14:51:19.369694939 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-07-19 14:51:17.058522697 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 14:51:19.696670382 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-07-19 14:51:21.043884514 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-07-19 14:51:21.988397041 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-07-19 14:51:23.043860827 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-07-19 14:51:23.048434382 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-07-19 15:00:29.941665260 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-07-19 15:00:42.328512263 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-07-19 15:00:43.931896785 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-07-19 15:00:51.936291513 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:00:51.942608587 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:00:53.933177844 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:00:53.937237017 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-07-19 15:00:53.941381293 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:00:53.946247887 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:00:53.952353437 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:00:53.959965249 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:01:03.933745824 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:03.938597011 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-07-19 15:01:07.933119800 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:01:07.937548078 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:01:11.948144585 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:11.956842594 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-07-19 15:01:21.933836978 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:01:21.938365605 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-07-19 15:01:29.868536049 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-07-19 15:01:31.930642634 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:01:33.698693980 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:33.701204578 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-07-19 15:01:33.703081064 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 14:59:59.105536572 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 14:59:59.851313123 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-07-19 14:59:59.855114101 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: local arbitration health state\" \"2021-07-19 15:01:39.929777640 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: local arbitration request-active state\" \"2021-07-19 15:01:39.931948274 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-07-19 15:01:41.843338174 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-07-19 15:01:08.253843974 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:08.809758206 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:01:08.837365260 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-07-19 15:01:09.742042907 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:01:09.752162710 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:09.815306709 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-07-19 15:01:09.982338592 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-07-19 15:01:09.989532010 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-07-19 15:01:10.029963905 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-07-19 15:01:10.035880384 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-07-19 15:01:10.040832454 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-07-19 15:01:31.051967996 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-07-19 15:01:31.056289991 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-07-19 15:01:31.060630177 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-07-19 15:01:31.065082873 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-07-19 15:02:33.887527486 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-07-19 17:42:30.174138784 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-07-19 17:42:30.174237977 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-07-19 21:18:38.342865470 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-07-19 21:18:38.342963199 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-07-19 21:18:58.191852169 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-07-19 21:18:58.191900715 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-07-19 21:23:43.068233800 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-07-19 21:23:43.792708597 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-07-19 21:23:48.735475225 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-07-19 21:23:48.735529758 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-07-19 21:23:50.174360083 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-07-19 21:23:50.174397296 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-07-19 21:23:51.066080977 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-07-19 21:23:51.069315211 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-07-19 21:23:51.789304267 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-07-19 21:23:51.793241018 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-07-19 21:23:57.164064383 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-07-19 21:23:57.750737986 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-07-19 21:24:17.231649722 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-07-19 21:24:17.258702621 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-07-19 21:24:51.252735988 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-07-19 21:24:51.269072003 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR WARNING \"Thermal fault detected in hardware\" \"2021-07-23 13:50:00.162804193 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-07-23 13:50:00.162848507 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-07-23 13:51:27.868455553 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-07-23 13:51:35.865166681 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-07-23 13:51:35.869318620 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault ASSERT WARNING \"Thermal fault detected in hardware\" \"2021-07-23 13:51:36.162019542 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-07-23 13:51:36.162087249 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-07-23 13:51:41.953615811 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-07-23 13:52:01.276357134 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-07-23 13:52:31.207625323 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: Fan Controller 1 Present\" \"2021-07-23 14:37:34.156865413 UTC\""
                },
                {
                    "log": "65794 controller-1 module-present EVENT NA \"Vfc1 removed\" \"2021-07-23 14:37:35.048706971 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: Fan Controller 1 Present\" \"2021-07-23 14:37:52.156826094 UTC\""
                },
                {
                    "log": "65794 controller-1 module-present EVENT NA \"Vfc1 present\" \"2021-07-23 14:37:53.045758564 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-02 14:10:20.180319244 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-02 14:10:22.978435503 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-02 14:10:24.312192009 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-02 14:10:24.630120169 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-02 14:10:24.635193601 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-02 14:10:30.610515241 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: local arbitration health state\" \"2021-08-02 14:10:22.330302517 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-02 14:10:22.331716255 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-02 14:10:22.333040665 UTC\""
                },
                {
                    "log": "65538 controller-2 unknown-alarm EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-02 14:10:29.340883428 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=2\" \"2021-08-05 15:39:54.145246007 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-08-05 15:39:54.638774302 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-05 15:40:04.164293515 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-08-05 15:40:24.437382472 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-08-05 15:40:24.517678165 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-06 14:26:17.058609374 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-06 14:26:23.031342842 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-06 14:26:23.848623935 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-06 14:26:25.031729190 UTC\""
                },
                {
                    "log": "65796 psu-controller psu-redundancy-fault ASSERT WARNING \"PSU redundancy fault detected\" \"2021-08-06 14:27:13.548442939 UTC\""
                },
                {
                    "log": "65796 psu-controller psu-redundancy-fault EVENT NA \"Chassis doesnt have enough working power supplies\" \"2021-08-06 14:27:13.548485625 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-06 14:27:13.554982316 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-06 14:27:13.559122383 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-06 14:27:14.570636806 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-06 14:27:14.575318631 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-06 14:27:14.913842294 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-06 14:27:14.942032505 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-06 14:27:14.956037288 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-06 14:27:14.997543994 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-06 14:27:15.006584825 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-06 14:27:15.014876265 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-08-06 14:27:15.020889504 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-08-06 14:27:15.080388814 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-06 14:27:15.084759537 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-06 14:27:15.089018417 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-06 14:27:16.798599384 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-06 14:27:23.431881494 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-08-06 14:27:23.445519297 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-06 14:27:23.452535826 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-06 14:27:23.892798662 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-06 14:27:23.902618001 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-06 14:27:23.919876624 UTC\""
                },
                {
                    "log": "65796 psu-controller psu-redundancy-fault CLEAR WARNING \"PSU redundancy fault detected\" \"2021-08-06 14:27:24.397601474 UTC\""
                },
                {
                    "log": "65796 psu-controller psu-redundancy-fault EVENT NA \"Attribute health reset\" \"2021-08-06 14:27:24.397687258 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-08-06 14:34:27.681366189 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-06 14:34:34.051178653 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-06 14:34:36.049206349 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-08-06 14:34:36.052812882 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-06 14:34:37.772767793 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-06 14:34:37.777237085 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-06 14:34:37.781532609 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 14:47:57.636993726 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 14:48:05.634483558 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 14:48:05.637873223 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 14:48:11.782221510 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 14:48:16.437857151 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-08-06 14:48:16.558359566 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 14:48:24.440488578 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 14:48:24.504353115 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 14:48:30.519835494 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 14:48:30.921075182 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-08-06 14:48:52.657594990 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-06 14:48:52.697499540 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-06 14:49:26.712287266 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Attribute health reset\" \"2021-08-06 14:51:03.314798585 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 14:51:04.693230540 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 14:51:12.691292766 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 14:51:12.694881692 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 14:51:18.707557654 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 20:43:11.287969758 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 20:43:13.319985020 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 20:43:19.285063639 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 20:43:19.288409401 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 20:43:21.316189788 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 20:43:21.319674817 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 20:43:25.279462231 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 20:43:27.367149454 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-08-06 20:43:44.271836957 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-08-06 20:43:44.361641911 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-06 20:44:14.321026108 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-06 20:44:14.366673391 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-06 20:45:07.249388097 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-06 20:45:15.247412891 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-06 20:45:15.250954846 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-06 20:45:21.247617188 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has FEC corrected errors, value=0\" \"2021-08-06 20:45:24.254626927 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no FEC corrected errors\" \"2021-08-06 20:45:54.152284433 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-11 14:11:09.233172341 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-11 14:11:09.245776760 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-11 14:11:17.229518930 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-11 14:11:17.242566058 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-11 14:11:17.232856799 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-11 14:11:17.246036471 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-11 14:11:23.182123662 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-11 14:11:23.329166820 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-08-11 14:11:54.263322903 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-08-11 14:11:54.347352987 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-11 14:12:24.141399098 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-11 14:12:24.169613168 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-11 14:13:32.515070365 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-11 14:13:40.511386859 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-11 14:13:40.514752769 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-11 14:13:43.596640555 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-11 14:13:46.536492717 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-11 14:13:51.598347700 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-11 14:13:51.601811882 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=3\" \"2021-08-11 14:13:58.263451683 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-11 14:13:58.434321850 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-11 14:14:28.108494471 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=3\" \"2021-08-11 14:14:28.139906197 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-11 14:14:58.232804288 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-16 17:57:04.480782522 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-16 17:57:12.477793744 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-16 17:57:12.481299594 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-16 17:57:18.509405934 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-08-16 17:57:48.230098302 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-08-16 17:58:24.370724327 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-20 22:52:14.106466647 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-20 22:52:24.112569635 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-20 23:48:30.097908547 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-20 23:48:30.106386178 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-20 23:48:44.100349372 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-20 23:48:51.082582429 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-20 23:49:38.610702609 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-20 23:49:40.097536229 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-21 07:40:50.100109208 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-21 07:41:10.499379833 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-21 07:42:02.101366585 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-21 07:42:22.276450840 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-21 07:42:26.885146734 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-21 14:05:01.642792371 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-21 14:05:09.640976439 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-21 14:05:09.644438448 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-21 14:05:15.729180090 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-21 22:50:07.593175198 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-21 22:50:15.590865414 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-21 22:50:15.594253306 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-21 22:50:22.396482258 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-22 23:58:38.630358335 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-22 23:58:46.626956890 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-22 23:58:46.630162626 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-22 23:58:52.725586362 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault CLEAR INFO \"Thermal fault detected in hardware\" \"2021-08-26 21:29:46.635224062 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault CLEAR INFO \"Thermal fault detected in hardware\" \"2021-08-26 21:29:47.641808646 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-26 21:30:21.776390165 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-26 21:30:25.305917531 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-26 21:30:25.320828039 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-26 21:30:25.872094464 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-26 21:30:25.879807209 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-26 21:30:26.902484101 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-26 21:30:27.187659157 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-26 21:30:27.255434266 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-26 21:30:27.261858759 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-26 21:30:27.269506815 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-08-26 21:30:27.281346810 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-26 21:30:27.302181148 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-26 21:30:27.347860192 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-26 21:30:27.355149606 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-26 21:30:27.367642594 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-26 21:30:27.377169749 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-08-26 21:30:28.208454932 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-26 21:35:37.020367152 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-08-26 21:35:56.497141538 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-26 21:35:58.587773456 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-26 21:35:58.604819667 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-26 21:35:58.627950808 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-26 21:35:58.640155806 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-26 21:36:00.281012399 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-30 15:59:44.474240807 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-30 15:59:56.489892392 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-30 16:01:36.474015867 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-30 16:03:09.613042151 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-30 16:03:15.307613650 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-30 16:03:17.608834863 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-30 16:03:17.613227285 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-30 16:03:18.434915337 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-30 16:03:23.602248245 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-30 16:03:26.431212635 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-30 16:03:26.474093566 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-30 16:03:26.434750099 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-08-30 16:03:32.365101945 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-30 16:03:32.581602204 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-08-30 16:03:46.661659093 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-08-30 16:03:46.748416354 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-08-30 16:04:08.321695120 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-30 16:04:08.360292187 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-08-30 16:04:16.677707829 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-08-30 16:04:16.744287041 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-08-30 16:04:38.283786035 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-30 16:04:57.346140793 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-30 16:05:05.353157476 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-30 16:05:05.356697900 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-30 16:05:06.474196185 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-30 16:05:11.524898425 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=3\" \"2021-08-30 16:05:22.833600365 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=3\" \"2021-08-30 16:05:42.329818620 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-08-30 16:05:58.700368851 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-30 16:06:12.131717149 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-08-30 22:40:45.814288773 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-08-30 22:40:46.129109411 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-30 22:40:46.475643437 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-30 22:40:46.483808936 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-30 22:40:48.129027375 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-30 22:40:48.133731151 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-08-30 22:40:50.710836187 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-30 22:40:56.585396795 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-30 22:41:49.036975110 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-31 19:16:49.103139059 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update is running for lop app\" \"2021-08-31 19:16:51.099284158 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-31 19:17:57.022437698 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-08-31 19:17:57.026681298 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-08-31 19:17:57.032111152 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc1 present\" \"2021-08-31 19:18:02.187273899 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Vpc2 present\" \"2021-08-31 19:18:02.202754110 UTC\""
                },
                {
                    "log": "65794 controller-2 module-present EVENT NA \"Lcd present\" \"2021-08-31 19:18:02.459854302 UTC\""
                },
                {
                    "log": "65550 controller-2 firmware-update-status EVENT NA \"Firmware update completed for lop app\" \"2021-08-31 19:17:56.837131783 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-08-31 19:18:05.451538684 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-08-31 19:18:29.581696327 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-08-31 19:18:33.113843125 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-08-31 19:18:33.131143631 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-08-31 19:18:33.141230320 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-08-31 19:18:33.146735265 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-08-31 19:19:06.850387705 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-31 19:23:59.099103688 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-08-31 19:23:51.188938038 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-08-31 19:23:51.193597794 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-08-31 19:23:51.197224440 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-31 19:23:58.933461247 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-31 19:23:58.944286574 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-08-31 19:23:58.951811511 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-08-31 19:23:58.957311927 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update is running for lop app\" \"2021-08-31 19:24:00.928829024 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-08-31 19:24:41.499929660 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-08-31 19:25:07.103585800 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Attribute health reset\" \"2021-08-31 19:25:07.483247691 UTC\""
                },
                {
                    "log": "65794 controller-1 module-present EVENT NA \"Lcd present\" \"2021-08-31 19:25:08.947830938 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update is running for lop <no value>\" \"2021-08-31 19:25:09.588264128 UTC\""
                },
                {
                    "log": "65550 controller-1 firmware-update-status EVENT NA \"Firmware update completed for lop app\" \"2021-08-31 19:25:10.813261240 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-08-31 19:25:16.941812489 UTC\""
                },
                {
                    "log": "65794 controller-1 module-present EVENT NA \"Vpc1 present\" \"2021-08-31 19:25:16.975802066 UTC\""
                },
                {
                    "log": "65794 controller-1 module-present EVENT NA \"Vpc2 present\" \"2021-08-31 19:25:17.054144539 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-31 19:59:17.303701871 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-31 19:59:29.107943778 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-08-31 19:59:35.102864414 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-31 20:03:25.555662924 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-31 20:03:25.808146489 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-08-31 20:03:26.850548387 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-31 20:03:33.097963881 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-31 20:03:33.559162364 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-31 20:03:33.562755164 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-31 20:03:33.803826378 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-31 20:03:33.807188096 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-08-31 20:03:34.847733534 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-08-31 20:03:34.851239601 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-31 20:03:35.097214538 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-08-31 20:03:37.097130883 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-31 20:03:39.747059451 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-31 20:03:40.814716884 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-08-31 20:03:41.552913236 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-08-31 20:03:45.286214257 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-08-31 20:03:45.300271024 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-08-31 20:03:45.378091715 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-08-31 20:03:58.335868556 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-08-31 20:03:58.400738870 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-08-31 20:03:58.437626402 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-08-31 20:04:21.254186816 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-08-31 20:04:21.315543662 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-08-31 20:04:21.335978194 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-08-31 20:04:32.350520076 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-08-31 20:04:32.398356586 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-08-31 20:04:32.437533188 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 00:32:01.099366592 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 00:32:01.104131370 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 00:48:25.097530372 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 00:53:35.176312613 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 00:53:36.421338720 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 00:53:43.099081732 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 00:53:43.104241973 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 00:53:43.173755095 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 00:53:43.177047802 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 00:53:44.416926588 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 00:53:44.420503202 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 00:53:49.312051939 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 00:53:50.546122971 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-02 00:53:52.350320215 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-02 00:53:52.422889464 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 00:54:00.487619944 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-09-02 00:54:01.316532768 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-02 00:54:01.357103859 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 00:54:07.098939849 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 00:54:08.485624564 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 00:54:08.489094626 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 00:54:14.615458136 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-02 00:54:28.316389792 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-02 00:54:28.369808436 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-09-02 00:54:28.396285011 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-02 00:54:35.194843420 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-02 00:54:35.224803935 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-09-02 00:54:35.233346285 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-09-02 00:54:58.337576162 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-09-02 00:55:11.283406578 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 01:49:45.206746568 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 01:49:45.217340604 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-02 01:49:45.222273095 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"control plane switch port xe5 (blade2/ctrlplane02) has uncorrected errors, value=3\" \"2021-09-02 01:52:05.195707738 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"control plane switch port xe5 (blade2/ctrlplane02) has no uncorrected errors\" \"2021-09-02 01:52:35.176053361 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 01:55:23.646177888 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 01:55:23.664074071 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 01:55:29.642849022 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 01:55:31.098351097 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 01:55:31.642092682 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 01:55:31.661288457 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 01:55:31.664779615 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 01:55:33.134772505 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 01:55:37.642175815 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 01:55:37.827873528 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-02 01:55:48.359558037 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-02 01:55:48.378151014 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-09-02 01:55:51.241880788 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-02 01:55:51.270733879 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-02 01:55:51.336646320 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-02 01:55:57.096923419 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-02 01:55:59.266978276 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-02 01:55:59.270350931 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-02 01:56:05.267722471 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-02 01:56:22.352457939 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-09-02 01:56:22.383477737 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-02 01:56:22.423429602 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-09-02 01:56:25.265301154 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-02 01:56:25.308325328 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-02 01:56:25.419900230 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-09-02 01:56:52.331669432 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-09-02 01:56:55.241052337 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-10 14:42:27.100658134 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-10 14:42:27.106803459 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-10 14:43:54.927411818 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-10 14:46:48.105956894 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-10 14:46:48.110218796 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-10 14:46:48.117374397 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-10 14:46:49.216888828 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-10 14:47:04.317917952 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-10 14:47:05.698806371 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-10 14:47:05.704154213 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-10 14:47:05.710092222 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-10 14:47:07.243204218 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-10 14:46:48.184447409 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-10 14:46:48.201756783 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-10 14:46:48.205813212 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 output OK\" \"2021-09-10 14:46:48.235651962 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-10 14:46:50.183519719 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 input OK\" \"2021-09-10 14:46:50.197207550 UTC\""
                },
                {
                    "log": "65793 psu-3 psu-fault EVENT NA \"Deasserted: PSU 3 output OK\" \"2021-09-10 14:46:50.206616234 UTC\""
                },
                {
                    "log": "65793 psu-4 psu-fault EVENT NA \"Deasserted: PSU 4 input OK\" \"2021-09-10 14:46:50.211909650 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-10 14:47:04.183557168 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-10 14:47:04.188876019 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-09-10 14:47:04.193979156 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-10 14:47:09.888562150 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-10 14:48:11.021821824 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-11 15:59:13.665647052 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-11 15:59:13.687724097 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-11 15:59:14.172679843 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-11 15:59:16.567582199 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-11 15:59:20.565568959 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-11 15:59:21.470143024 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-11 15:59:21.474021329 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-11 16:00:57.245561729 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-11 16:00:57.432523865 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-11 16:00:57.439894607 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-11 16:00:58.967720687 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-11 16:01:01.751129084 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-11 16:01:01.758037020 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-11 16:01:01.764069895 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-11 16:01:02.860850736 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-11 16:01:02.865071149 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-11 16:01:02.868752061 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-11 16:01:02.995179719 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-11 16:01:03.001379012 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-11 16:01:03.006947853 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-11 16:01:04.402970730 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-11 16:01:04.411819901 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-11 16:01:04.472127515 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-11 16:01:05.413882439 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-11 16:01:05.449957038 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-11 16:01:05.637814714 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-11 16:01:09.100981062 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-11 16:01:05.605671365 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-11 16:01:05.610267162 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-11 16:01:05.616286273 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-11 16:01:09.675779621 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-11 16:01:11.587828580 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-11 16:01:11.592235226 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-09-11 16:01:11.596156502 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-11 16:02:01.103508359 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-15 02:07:46.119051551 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-15 02:07:47.587992469 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-15 02:07:47.592349721 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-15 02:07:47.601813204 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-15 02:08:03.196667921 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-15 02:08:05.434787459 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-15 02:08:05.448425785 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-15 02:08:05.458645855 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 02:08:06.759771621 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 02:08:06.774858856 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-15 02:08:07.871778525 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-15 02:08:12.159246170 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-15 02:08:04.567990949 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-15 02:07:46.562410417 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-15 02:08:08.569499435 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-15 02:07:46.567047920 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-15 02:07:46.571206554 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-15 02:07:49.678809031 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 02:08:02.564080211 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 02:08:02.568570803 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-09-15 02:08:02.572786196 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 02:55:23.490883609 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 02:55:25.505109967 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 02:55:31.489211133 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 02:55:31.492737989 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 02:55:31.596489085 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 02:55:31.614475278 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 02:55:33.503417412 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 02:55:33.506886122 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 02:55:37.384246240 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 02:55:37.444215752 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 02:55:39.530979897 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 02:55:45.383523322 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 02:55:45.387046286 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 02:55:45.630443182 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 02:55:51.859269608 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 03:02:13.326783840 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 03:02:23.100654624 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 03:03:36.088035670 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 03:03:39.884200919 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 03:03:44.985645736 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 03:03:45.881301095 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 03:03:47.880436914 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 03:03:50.078878703 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 03:03:52.983180942 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 03:03:52.986706016 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 03:03:53.752891319 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 03:03:54.088263671 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has FEC corrected errors, value=0\" \"2021-09-15 03:03:57.223656494 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-15 03:03:57.227921001 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 03:03:58.929712398 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-15 03:04:06.375084022 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-09-15 03:04:06.469841707 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-15 03:04:27.288945259 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no FEC corrected errors\" \"2021-09-15 03:04:27.310081688 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-15 03:04:27.313029840 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-15 03:04:36.262094008 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-15 03:04:36.293202605 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-15 03:05:01.138217774 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-15 03:05:03.244646536 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-15 03:05:09.137510432 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-15 03:05:09.140894352 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-15 03:05:10.090995159 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-15 03:05:15.141248605 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-09-15 03:05:33.331216069 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-09-15 03:05:46.141009525 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-09-15 03:06:07.258303972 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-09-15 03:06:20.281608676 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 19:05:08.602659067 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 19:05:08.912065247 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-15 19:45:20.080635023 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-15 19:47:58.818190579 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-15 19:47:58.822953006 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-15 19:47:58.828402623 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-15 19:47:58.832529962 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-15 19:48:13.787397182 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-15 19:48:14.062098696 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-15 19:48:15.053443709 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-15 19:48:15.060524556 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-15 19:47:58.079338259 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-15 19:47:58.083669616 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-15 19:47:58.088079413 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-15 19:48:00.206373588 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 19:48:13.786232843 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 19:48:14.080534065 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-09-15 19:48:14.085476723 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-15 19:48:18.016640089 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-15 19:48:34.029014982 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-15 19:48:50.040774415 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-15 19:48:54.095961001 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 19:48:54.195984395 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 19:48:55.077052155 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 19:48:55.084653306 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 19:48:55.102744426 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 19:48:55.130570953 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-15 19:48:55.222375229 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-15 19:48:56.052943594 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-15 19:48:56.068123868 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-15 19:48:56.094013897 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-15 19:48:58.047783885 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-15 19:49:02.064488392 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-15 19:49:20.198689425 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-16 00:05:29.058878783 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-16 00:09:55.053262698 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-16 00:09:56.219127608 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-16 00:12:38.114601141 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-16 00:12:38.120043988 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-16 00:12:38.533762362 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-16 00:12:38.541067376 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-16 00:12:38.545651337 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-16 00:13:24.074216897 UTC\""
                },
                {
                    "log": "131073 chassis nebsDisabled EVENT NA \"Chassis is operating with non-NEBS temperature thresholds\" \"2021-09-16 00:12:36.871372715 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-16 00:16:50.822372173 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-16 00:16:47.474861151 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-16 00:16:47.480637868 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-16 00:16:47.505037604 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-16 00:16:47.636742983 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration grant-active state\" \"2021-09-16 00:16:47.642026507 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-16 00:16:49.621244217 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 00:36:38.229066172 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 00:36:46.224866988 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 00:36:46.228268499 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 00:36:46.292260647 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 00:36:52.259784610 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 00:37:01.344126266 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 00:37:06.414676452 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 00:37:09.342377503 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 00:37:09.345881289 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 00:37:10.128835429 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 00:37:14.119945262 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 00:37:14.413076458 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 00:37:14.416393400 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 00:37:15.513724991 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 00:37:20.536213233 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-16 01:05:52.982256174 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 01:07:19.930506699 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 01:07:27.927662118 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 01:07:27.931055017 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 01:07:28.112899570 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 01:07:33.893290440 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-09-16 01:07:56.329948227 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-16 01:08:07.721719789 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-16 01:08:20.556262573 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-16 01:08:30.338905872 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-16 01:08:43.885922797 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-16 01:09:46.193052146 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 01:09:47.751904828 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 01:09:55.748483296 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 01:09:55.751840011 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 01:09:56.347740953 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 01:10:01.696982417 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has FEC corrected errors, value=0\" \"2021-09-16 01:10:10.409686354 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-16 01:10:10.413616818 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-16 01:10:23.168760323 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no FEC corrected errors\" \"2021-09-16 01:10:40.345814384 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-16 01:10:40.353708403 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-16 01:10:57.814319031 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-16 01:11:13.245547645 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-16 01:11:21.244449188 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-16 01:11:21.248087317 UTC\""
                },
                {
                    "log": "65546 blade-3 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-16 01:11:22.112823354 UTC\""
                },
                {
                    "log": "65550 blade-3 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-16 01:11:27.252135436 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has FEC corrected errors, value=0\" \"2021-09-16 01:11:33.800923906 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has uncorrected errors, value=1\" \"2021-09-16 01:11:33.806870406 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has uncorrected errors, value=1\" \"2021-09-16 01:11:46.321368310 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no FEC corrected errors\" \"2021-09-16 01:12:07.842860124 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp0) has no uncorrected errors\" \"2021-09-16 01:12:07.846123443 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg11 (blade3/bp1) has no uncorrected errors\" \"2021-09-16 01:12:20.299310957 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-17 02:31:56.112560409 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-17 02:37:58.112461846 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-17 02:37:57.613331309 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-17 02:37:57.617901077 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-17 02:38:04.112360525 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-17 02:38:05.606951504 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-17 02:38:06.024007937 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-17 02:38:06.124063877 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-17 02:38:06.140664820 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-17 02:38:07.608377425 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-17 02:38:08.156980575 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration grant-active state\" \"2021-09-17 02:38:08.184759894 UTC\""
                },
                {
                    "log": "131072 chassis nebsEnabled EVENT NA \"Chassis is operating with NEBS temperature thresholds\" \"2021-09-17 02:39:06.617825248 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration request-active state\" \"2021-09-17 02:39:08.309610675 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-17 02:39:08.334882754 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-17 02:39:08.403971225 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-17 02:39:08.630702164 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration request-active state\" \"2021-09-17 02:39:08.644066334 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: local arbitration grant-active state\" \"2021-09-17 02:39:08.659704584 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-17 02:39:23.821545998 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-17 02:39:23.830099427 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-17 02:39:23.875213232 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-17 02:39:23.901718526 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration request-active state\" \"2021-09-17 02:39:25.091276676 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-17 02:39:25.103911182 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration request-active state\" \"2021-09-17 02:39:25.124389459 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: local arbitration grant-active state\" \"2021-09-17 02:39:25.131677177 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-17 02:39:25.230597480 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Deasserted: peer arbitration health state\" \"2021-09-17 02:39:25.565629648 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Deasserted: local arbitration health state\" \"2021-09-17 02:45:15.022931709 UTC\""
                },
                {
                    "log": "66048 controller-2 arbitration-state EVENT NA \"Asserted: local arbitration health state\" \"2021-09-17 02:45:41.018475162 UTC\""
                },
                {
                    "log": "66048 controller-1 arbitration-state EVENT NA \"Asserted: peer arbitration health state\" \"2021-09-17 02:45:41.506441647 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-22 22:56:37.486455051 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-22 22:56:37.677341423 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-23 15:41:13.546260445 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-23 15:41:14.492257919 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-23 15:41:19.606599321 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-23 15:41:21.546814292 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-23 15:41:21.550001477 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-23 15:41:21.609291358 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-23 15:41:22.487765071 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-23 15:41:22.491216997 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-23 15:41:27.632195955 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-23 15:41:28.367928479 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=2\" \"2021-09-23 15:41:35.180572498 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-23 15:41:35.192570452 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-23 15:41:41.763979499 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-23 15:41:41.820487815 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-23 15:42:11.146114439 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-23 15:42:11.234165579 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-23 15:42:17.909720378 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-23 15:42:17.928674548 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-24 04:59:09.606568383 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Deasserted: VQF hot\" \"2021-09-24 04:59:29.222759436 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-24 05:03:33.184027963 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for vqf 0\" \"2021-09-24 05:03:37.267592518 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-24 05:03:41.180713810 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-24 05:03:41.183910137 UTC\""
                },
                {
                    "log": "65546 blade-1 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-24 05:03:41.619581473 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for vqf 0\" \"2021-09-24 05:03:45.265730176 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update is running for atse 0\" \"2021-09-24 05:03:45.269210341 UTC\""
                },
                {
                    "log": "65546 blade-2 thermal-fault EVENT NA \"Asserted: VQF hot\" \"2021-09-24 05:03:45.607067359 UTC\""
                },
                {
                    "log": "65550 blade-1 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-24 05:03:47.154697558 UTC\""
                },
                {
                    "log": "65550 blade-2 firmware-update-status EVENT NA \"Firmware update completed for atse 0\" \"2021-09-24 05:03:51.281899368 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has uncorrected errors, value=1\" \"2021-09-24 05:04:01.218078879 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has uncorrected errors, value=1\" \"2021-09-24 05:04:01.266796890 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has uncorrected errors, value=1\" \"2021-09-24 05:04:11.652816942 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has uncorrected errors, value=1\" \"2021-09-24 05:04:11.722160589 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp1) has no uncorrected errors\" \"2021-09-24 05:04:35.220576692 UTC\""
                },
                {
                    "log": "66049 controller-2 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp1) has no uncorrected errors\" \"2021-09-24 05:04:35.233741034 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg14 (blade2/bp0) has no uncorrected errors\" \"2021-09-24 05:04:41.769723646 UTC\""
                },
                {
                    "log": "66049 controller-1 switch-status EVENT NA \"data plane switch port hg0 (blade1/bp0) has no uncorrected errors\" \"2021-09-24 05:04:41.835173884 UTC\""
                }
            ]
        }
    }



System Health
=============

VELOS also has a very robust **system health** utility where all the various hardware and software subsystems will first provide a high level health status, but then deeper detail can be gained on what is monitored via each subsystem.


------------------------------
Checking System Health via GUI
------------------------------



------------------------------
Checking System Health via API
------------------------------

