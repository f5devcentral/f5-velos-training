====================================
Securing / Hardening F5OS on VELOS
====================================

F5OS tenants follow the standard hardening/security best practices that are outlined in the following solution article:

`K53108777: Hardening your F5 system <https://support.f5.com/csp/article/K53108777>`_

This section will focus on how to harden/secure the F5OS layer of the VELOS chassis. 

F5OS Platform Layer Isolation
=============================

When looking at management of the VELOS platform, it is important to separate the in-band (data plane) networking from the out-of-band (management) networking. Management of the new F5OS platform layer is completely isolated from in-band data-plane traffic, networking, and VLANs and is managed via the out-of-band management network only. It is purposely isolated so that it is only accessible via the out-of-band management network. In fact, there are no in-band (data-plane) IP addresses assigned to the F5OS layer, only tenants will have in-band (data-plane) IP addresses and access. Tenants also have out-of-band connectivity so they can be managed via the out-of-band network.

This allows customers to run a secure/locked-down out-of-band management network where access is tightly restricted. The diagram below shows the out-of-band management access entering the VELOS chassis through management ports on the system controllers (1/mgmt0 and 2/mgmt0). The external management ports are bridged to an internal out-of-band network that connects to all tenants, chassis partitions and blades within the VELOS chassis. 

.. image:: images/velos_security/image1.png
  :align: center

Out-of-Band Management Network
==============================

All out-of-band networking for VELOS is handled through the system controllers. Each system controller has its own static IP address, and there is also a floating IP address that will follow the active system controller. The system controller will also act as a bridge between the outside out-of-band network, and the out-of-band management VLAN inside the chassis. By default, there is one common network/VLAN for out-of-band networking inside the chassis. All chassis partitions, and tenants will connect to this VLAN, and their default gateway should be pointed to a router on the outside of the chassis. You can attempt to isolate partitions and tenants on the OOB network by using separate IP networks that are multi-netted, but this does not provide true network isolation that a VLAN would provide. 

In F5OS-C 1.8.0, 802.1Q VLAN tagging support was added for the out-of-band management ports on VELOS. This new option allows for system controllers, chassis partitions and tenants to be assigned to specific VLANs. This will allow for greater separation on the management VLAN which in previous releases had to be a single shared VLAN. The external ports are configured with specific tagged or untagged VLANs and then those VLANs are presented to the system controllers, partitions, and tenants as untagged, meaning no special configuration is needed to convert to tagged management VLANs inside tenants.  

.. image:: images/velos_networking/tagged-vlans.png
  :align: center

Allow List for F5OS Management
===============================

F5OS only allows management access via the out-of-band management interfaces on VELOS, there is no in-band access to the F5OS management layer. Within VELOS there are two layers for F5OS; the **system controller** layer, and the **chassis partition** layer. Each of these layers have their own management IP addresses, and access control which can restrict access through the out-of-band network. 

Each of the two system controllers has a static IP address assigned, and there is a floating IP address which should be used to access the active system controller. As chassis partitions are deployed, they also have a single IP address which is assigned. Access to those F5OS management interfaces may be restricted to specific IP addresses (both IPv4 and IPv6), subnets (via Prefix Length), as well as protocols - 443 (HTTPS), 80 (HTTP), 8888 (RESTCONF), 161 (SNMP), 7001 (VCONSOLE), and 22 (SSH) with version F5OS-C 1.6.0 and later. An administrator can add one or more Allow List entries via the CLI, webUI (webUI will be added in F5OS-C 1.7.0) or API at the system controller layer and the chassis partition layer to lock down access to specific endpoints.

By default, all ports except for 161 (SNMP) are enabled for access, meaning ports 80, 443, 8888, 7001, and 22 are allowed access. Port 80 is only open to allow a redirect to port 443 in case someone tries to access the webUI over port 80. The webUI itself is not accessible over port 80. Port 161 is typically viewed as un-secure and is therefore not accessible until an allow list entry is created for the endpoint trying to access F5OS using SNMP queries. Ideally SNMPv3 should be utilized to provide additional layers of security on an otherwise un-secure protocol. VCONSOLE access also must be explicitly configured before access to the tenants is possible over port 7001. 

To further lock down access you may add an Allow List entry including an IP address and optional prefix for each of the protocols listed above. As an example, if you wanted to restrict API and webUI access to a particular IP address and/or subnet, you could add an Allow List entry for the desired IP or subnet (using the prefix length), specify port 443 and all access from other IP endpoints will be prevented.

The examples below can be applied at either the system controller layer logging in using the floating system controller IP address or pointing to it for API calls, or to any chassis partition using its management IP address when logging in or sending API calls. 

Adding Allow List Entries via CLI
-----------------------------------

If you would like to lock down one of the protocols to either a single IP address or subnet, use the **system allowed-ips** command. Be sure to commit any changes. The **prefix-length** parameter is optional. If you omit it, then you will lock down access to a specific IP endpoint, if you add it, you can lock down access to a specific subnet.

.. code-block:: bash

    syscon-2-active(config)# system allowed-ips allowed-ip snmp config ipv4 address 10.255.0.0 prefix-length 24 port 161 
    syscon-2-active(config-allowed-ip-snmp)# commit
    Commit complete.
    syscon-2-active(config-allowed-ip-snmp)#

Currently you can add one IP address/port pair per **allowed-ip** name with an optional prefix length to specify a CIDR block containing multiple addresses. If you require more than one non-contiguous IP address or subnets, you can add it under another name as seen below. 

.. code-block:: bash

    syscon-2-active(config)# system allowed-ips allowed-ip SNMP-144 config ipv4 address 10.255.0.144 port 161 
    syscon-2-active(config-allowed-ip-SNMP-144)# commit
    Commit complete.
    syscon-2-active(config-allowed-ip-SNMP-144)#


    syscon-2-active(config)# system allowed-ips allowed-ip SNMP-145 config ipv4 address 10.255.2.145 port 161
    syscon-2-active(config-allowed-ip-SNMP-145)# commit
    Commit complete.
    syscon-2-active(config-allowed-ip-SNMP-145)#


Adding Allow List Entries via API
-----------------------------------

Below is an example of allowing multiple SNMP endpoints (port 161) to query SNMP on the F5OS platform layer.

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-allowed-ips:allowed-ips

Within the body of the API call, specific IP address/port, and optional prefix-length combinations can be added under a given name. In the current releases, you are limited to one IP address/port/prefix per name. 

.. code-block:: json

    {
        "allowed-ip": [
            {
                "name": "SNMP-142",
                "config": {
                    "ipv4": {
                        "address": "10.255.0.142",
                        "prefix-length": "32",
                        "port": 161
                        
                    }
                }
            },
            {
                "name": "SNMP-143",
                "config": {
                    "ipv4": {
                        "address": "10.255.0.143",
                        "prefix-length": "32",
                        "port": 161
                    }
                }
            },
            {
                "name": "SNMP-144",
                "config": {
                    "ipv4": {
                        "address": "10.255.0.144",
                        "prefix-length": "32",
                        "port": 161
                    }
                }
            }
        ]
    }



To view the allowed IPs in the API, use the following call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-allowed-ips:allowed-ips

The output will show the previously configured allowed-ips.


.. code-block:: json

    {
        "f5-allowed-ips:allowed-ips": {
            "allowed-ip": [
                {
                    "name": "SNMP-142",
                    "config": {
                        "ipv4": {
                            "address": "10.255.0.142",
                            "prefix-length": "32",
                            "port": 161
                        }
                    }
                },
                {
                    "name": "SNMP-143",
                    "config": {
                        "ipv4": {
                            "address": "10.255.0.143",
                            "prefix-length": "32",
                            "port": 161
                        }
                    }
                },
                {
                    "name": "SNMP-144",
                    "config": {
                        "ipv4": {
                            "address": "10.255.0.144",
                            "prefix-length": "32",
                            "port": 161
                        }
                    }
                }
            ]
        }
    }

Adding Allow List Entries via webUI
-----------------------------------

You can configure the **Allow List** in the webUI starting with version F5OS-C 1.7.0 under the **System Settings** section. 

.. image:: images/velos_security/image2.png
  :align: center
  :scale: 70%

Below is an example of allowing any SNMP endpoint at 10.255.0.0 (prefix length of 24) to query the F5OS layer on port 161.

.. image:: images/velos_security/image3.png
  :align: center
  :scale: 70%

In later versions, the allow list configuration is now under the **System Settings -> System Security** page.

.. image:: images/velos_security/system-security-allow.png
  :align: center
  :scale: 70%

Setting F5OS Primary Key
======================== 

The F5 VELOS system uses a primary key to encrypt highly sensitive passwords/passphrases in the configuration database, such as:

- Tenant unit keys used for TMOS Secure Vault
- The F5OS API Service Gateway TLS key
- Stored iHealth credentials
- Stored AAA server credentials

The primary key is randomly generated by F5OS during initial installation. You should set the primary key to a known value prior to performing a configuration backup. If you restore a configuration backup on a different VELOS device, e.g. during an RMA replacement, you must first set the primary key passphrase and salt on the destination device to the same value as the source device. If this is not done correctly, the F5OS configuration restoration may appear to succeed but produce failures later when the system attempts to decrypt and use the secured parameters.

You should periodically change the primary key for additional security. If doing so, please note that a configuration backup is tied to the primary key at the time it was generated. If you change the primary key, you cannot restore older configuration backups without first setting the primary key to the previous value, if it is known. More details are provided in the solution article below.


`K50135154: Backup and restore the F5OS-C configuration on a VELOS system <https://my.f5.com/manage/s/article/K50135154>`_

To set the primary-key issue the following command in config mode.

.. code-block:: bash

    syscon-1-active(config)# system aaa primary-key set passphrase               
    Value for 'passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    Value for 'confirm-passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    Value for 'salt' (<string, min: 6 chars, max: 255 chars>): **************
    Value for 'confirm-salt' (<string, min: 6 chars, max: 255 chars>): **************
    response Info: Key migration is initiated. Use 'show system aaa primary-key state status' to get status

    syscon-1-active(config)#

You can view the status of the primary-key being set with the **show system aaa primary-key state status** CLI command.

.. code-block:: bash

    syscon-1-active# show system aaa primary-key state status
    system aaa primary-key state status "IN_PROGRESS        Initiated: Tue Apr  9 19:46:14 2024"
    syscon-1-active# show system aaa primary-key state status
    system aaa primary-key state status "COMPLETE        Initiated: Tue Apr  9 19:46:14 2024"
    syscon-1-active# 

Note that the hash key can be used to check and compare the status of the primary-key on both the source and the replacement devices if restoring to a different device. To view the current primary-key hash, issue the following CLI command.

.. code-block:: bash

    syscon-2-active# show system aaa primary-key 
    system aaa primary-key state hash sj2GslitH9XY14h/cpY0TJhMWkU+CpvAU9vxxiL4aZcfE6qnSUDU3PWx+lCZO5KrqVzlWu/3mRugCNniNyQhSA==
    system aaa primary-key state status NONE
    syscon-2-active#


Certificates for Device Management
==================================

F5OS supports TLS device certificates and keys to secure connections to the management interface. You can either create a self-signed certificate or load your own certificates and keys into the system. In F5OS-C 1.6.0 an admin can now optionally enter a passphrase with the encrypted private key. More details can be found in the link below.

`VELOS Certificate Management Overview <https://techdocs.f5.com/en-us/velos-1-5-0/velos-systems-administration-configuration/title-system-settings.html#cert-mgmt-overview>`_


Managing Device Certificates, Keys, CSRs, and CAs via CLI
--------------------------------------------------------

By default, F5OS uses a self-signed certificate and key for device management. If you would like to create your own private key and self-signed certificate, use the following CLI command:

.. code-block:: bash

    syscon-2-active(config)# system aaa tls create-self-signed-cert name jim email jim@f5.com city Boston region MA country US organization F5 unit Sales version 1 days-valid 365 key-type encrypted-ecdsa curve-name secp384r1 store-tls true key-passphrase 
    Value for 'key-passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    Value for 'confirm-key-passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    syscon-2-active(config)#


The **store-tls** option when set to **true**, stores the private key and self-signed certificate in the system instead of returning the values only in the CLI output. If you would prefer to have the keys returned in the CLI output and not stored in the system, then set **store-tls false** as seen below.

.. code-block:: bash

    syscon-2-active(config)# system aaa tls create-self-signed-cert name jim email jim@f5.com city Boston region MA country US organization F5 unit Sales version 1 days-valid 365 key-type encrypted-ecdsa curve-name secp384r1 store-tls false key-passphrase
    Value for 'key-passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    Value for 'confirm-key-passphrase' (<string, min: 6 chars, max: 255 chars>): **************
    key-response 
    -----BEGIN EC PRIVATE KEY-----
    Proc-Type: 4,ENCRYPTED
    DEK-Info: AES-256-CBC,6EE0AFCBE422562DD8653DA0EA60B3AA

    TignlV9B8xZj1Pr9/NZrlwZkjhfa1Md1pksZZ5pCxUXkQBJjj/XN9bve6E9ZwZlw
    /mh7Anv46XZikh6PHKIbdtQNTiTfVljpXSD2xn1nAlPjUlE8xg5H1FXEs4KTDjvN
    NSDd3lupIH7xq37/3iMyKx2hWyZbMCaSPVMPyt4wBMZ2TucaOx0HEK1YOpjWyPV9
    DPF2J5q3srSDd7RAti6hQ3R214y65TT4uPPxcZviz+s=
    -----END EC PRIVATE KEY-----

    cert-response 
    -----BEGIN CERTIFICATE-----
    MIICDzCCAZUCCQDsONxeyLMipDAKBggqhkjOPQQDAjBxMQwwCgYDVQQDDANqaW0x
    CzAJBgNVBAYTAlVTMQswCQYDVQQIDAJNQTEPMA0GA1UEBwwGQm9zdG9uMQswCQYD
    VQQKDAJGNTEOMAwGA1UECwwFU2FsZXMxGTAXBgkqhkiG9w0BCQEWCmppbUBmNS5j
    b20wHhcNMjMwNTA0MTY1NjUwWhcNMjQwNTAzMTY1NjUwWjBxMQwwCgYDVQQDDANq
    aW0xCzAJBgNVBAYTAlVTMQswCQYDVQQIDAJNQTEPMA0GA1UEBwwGQm9zdG9uMQsw
    CQYDVQQKDAJGNTEOMAwGA1UECwwFU2FsZXMxGTAXBgkqhkiG9w0BCQEWCmppbUBm
    NS5jb20wdjAQBgcqhkjOPQIBBgUrgQQAIgNiAAQa1U/Nlxqj2+8WeXFH9sFtzKx9
    i63GXFVPMAJ6B8YvPMPRJWBhMfKFf93LdB6en5t3AGkApRkzCxmNMunknmrLOJqL
    apjsUQFznt2ksk0EO3c8+lxe80/dfiJs7e6jWygwCgYIKoZIzj0EAwIDaAAwZQIx
    AK91V3pXXWc0grzWu8V9c1Ls8pUESMk/02cHbQ4KpHy9dIM7Urqv4eOz2/7KPHYL
    PgIwMeuPVPB3kmata305fN7XGI+vu9bbKU2SUBXV55YRF5qGmyURLZJr8/tMkRlB
    Z5lL
    -----END CERTIFICATE-----
    syscon-2-active(config)# 


The management interface will now use the self-signed certificate you just created. You can verify by connecting to the F5OS management interface via a browser and then examining the certificate.

.. image:: images/velos_security/imagecert.png
  :align: center
  :scale: 70%


To create a Certificate Signing Request (CSR) via the CLI use the **system aaa tls create-csr** command.

.. code-block:: bash

    syscon-2-active(config)# system aaa tls create-csr name r10900-1.f5demo.net email jim@f5.com city Boston country US organization F5 region MA unit Sales version 1 
    response 
    -----BEGIN CERTIFICATE REQUEST-----
    MIIBejCCAQECAQEwgYExHDAaBgNVBAMME3IxMDkwMC0xLmY1ZGVtby5uZXQxCzAJ
    BgNVBAYTAlVTMQswCQYDVQQIDAJNQTEPMA0GA1UEBwwGQm9zdG9uMQswCQYDVQQK
    DAJGNTEOMAwGA1UECwwFU2FsZXMxGTAXBgkqhkiG9w0BCQEWCmppbUBmNS5jb20w
    djAQBgcqhkjOPQIBBgUrgQQAIgNiAARZs7Vj5RCTJQUbVe96+OwqaBmKqT7TL9Rs
    N2sFWtO8c/ze2fpYxV7yHSFSPQhw40fjmbxHb8O0BHtYIWP0czPTgnHA6l6CD4Q2
    qz0vvp8Q/wWtMrEo/lKbLBDKKe4FgvOgADAKBggqhkjOPQQDAgNnADBkAjAC3x6k
    Rg3ncd7B2U/7Fcclilv/xgUqVS9eXdkTZSqXiCIz4Ff7pOWwpkqHRBx5iLICMF0C
    tpSmmQVOSRYU98q8JJ0HClZ+8eTan2fotaKvYyzYMrge5cl0w6J7dnFZwLdmYA==
    -----END CERTIFICATE REQUEST-----
    syscon-2-active(config)# 


To create a CA bundle via the CLI use the **system aaa tls ca-bundle** command.

.. code-block:: bash

    syscon-2-active(config)# system aaa tls ca-bundles ca-bundle ?
    Possible completions:
    <Reference to configured name of the CA Bundle.>
    syscon-2-active(config)#  


To create a Client Revocation List (CRL) via the CLI issue the following command.

.. code-block:: bash

    syscon-2-active(config)# system aaa tls crls crl ?
    Possible completions:
    <Reference to configured name of the CRL.>
    syscon-2-active(config)# system aaa tls crls crl

You can display the current certificate, keys, and passphrases using the CLI command **show system aaa tls**.

.. code-block:: bash

    syscon-2-active# show system aaa tls
    system aaa tls state certificate Certificate:
                                        Data:
                                            Version: 1 (0x0)
                                            Serial Number:
                                                b9:0d:2d:10:75:4a:53:2f
                                        Signature Algorithm: ecdsa-with-SHA256
                                            Issuer: CN=jim, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Validity
                                                Not Before: May  4 16:55:58 2023 GMT
                                                Not After : May  3 16:55:58 2024 GMT
                                            Subject: CN=jim, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Subject Public Key Info:
                                                Public Key Algorithm: id-ecPublicKey
                                                    Public-Key: (384 bit)
                                                    pub: 
                                                        04:59:b3:b5:63:e5:10:93:25:05:1b:55:ef:7a:f8:
                                                        ec:2a:68:19:8a:a9:3e:d3:2f:d4:6c:37:6b:05:5a:
                                                        d3:bc:73:fc:de:d9:fa:58:c5:5e:f2:1d:21:52:3d:
                                                        08:70:e3:47:e3:99:bc:47:6f:c3:b4:04:7b:58:21:
                                                        63:f4:73:33:d3:82:71:c0:ea:5e:82:0f:84:36:ab:
                                                        3d:2f:be:9f:10:ff:05:ad:32:b1:28:fe:52:9b:2c:
                                                        10:ca:29:ee:05:82:f3
                                                    ASN1 OID: secp384r1
                                                    NIST CURVE: P-384
                                        Signature Algorithm: ecdsa-with-SHA256
                                            30:64:02:30:58:3a:be:8d:9e:e0:53:89:12:f2:10:b6:0b:f2:
                                            77:15:cb:eb:7d:55:31:01:70:4e:83:fc:89:f5:f5:e4:1a:4e:
                                            43:81:20:07:4a:0d:e3:72:3a:3e:7c:cb:54:67:b0:1a:02:30:
                                            1c:fe:7c:f1:a5:00:93:77:f2:02:af:82:fc:22:67:ea:35:e7:
                                            0e:9c:b8:90:13:f5:f8:98:f6:07:fe:f9:4b:66:99:32:e9:eb:
                                            92:3d:d2:a2:26:67:c9:01:f9:43:20:a6
                                    
    system aaa tls state verify-client false
    system aaa tls state verify-client-depth 1
    syscon-2-active# 



Managing Device Certificates, Keys, CSRs, and CAs via webUI
-----------------------------------------------------------

In the F5OS webUI you can manage device certificates for the management interface via the **Authentication & Access -> TLS Configuration** page. There are options to view the TLS certificates, keys, and details. You may also create self-signed certificates, create certificate signing requests (CSRs), and CA bundles.

.. image:: images/velos_security/imagecert2.png
  :align: center
  :scale: 70%

The screen below shows the options when creating a self-signed certificate. 

.. image:: images/velos_security/imagecert3.png
  :align: center
  :scale: 70%

If you choose the **Store TLS** option of **False** then the certificate details will be displayed, and you will be given the option to copy them to the clipboard. If you want to store them on the system, then set the **Store TLS** option to **True**.

.. image:: images/velos_security/imagecert4.png
  :align: center
  :scale: 70%

You can then use the **Show** options to display the current certificate, key, and details. Paste the text into the respective text boxes to add a certificate. TLS Key Passphrase is only required if TLS Key is in encrypted format. 

.. image:: images/velos_security/imagecert5.png
  :align: center
  :scale: 70%

.. image:: images/velos_security/imagecert6.png
  :align: center
  :scale: 70%

You can also create a Certificate Signing Request (CSR) for the self-signed certificate for use when submitting the certificate to the Certificate Authority (CA).

.. image:: images/velos_security/imagecsr1.png
  :align: center
  :scale: 70%

After clicking **Save** the CSR will appear, and you will be able to **Copy to Clipboard** so you can submit the signing request.

.. image:: images/velos_security/imagecsr2.png
  :align: center
  :scale: 70%

When you install an SSL certificate on the system, you also install a certificate authority (CA) bundle, which is a file that contains root and intermediate certificates. The combination of these two files completes the SSL chain of trust.

.. image:: images/velos_security/imageca1.png
  :align: center
  :scale: 70%

Managing Device Certificates, Keys, CSRs, and CAs via API
-------------------------------------

You can view the current certificates, keys and passphrases via the API using the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa/f5-openconfig-aaa-tls:tls

In the response you will notice the certificate, key, and optional passphrase as well as the state.

.. code-block:: json

    {
        "f5-openconfig-aaa-tls:tls": {
            "config": {
                "certificate": "-----BEGIN CERTIFICATE-----\nMIICEjCCAZcCCQDJefCyPp7SoTAKBggqhkjOPQQDAjByMQ0wCwYDVQQDDARqaW0y\nMQswCQYDVQQGEwJVUzELMAkGA1UECAwCTUExDzANBgNVBAcMBkJvc3RvbjELMAkG\nA1UECgwCRjUxDjAMBgNVBAsMBVNhbGVzMRkwFwYJKoZIhvcNAQkBFgpqaW1AZjUu\nY29tMB4XDTIzMDIyNDIxMzUzMVoXDTI0MDIyNDIxMzUzMVowcjENMAsGA1UEAwwE\namltMjELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAk1BMQ8wDQYDVQQHDAZCb3N0b24x\nCzAJBgNVBAoMAkY1MQ4wDAYDVQQLDAVTYWxlczEZMBcGCSqGSIb3DQEJARYKamlt\nQGY1LmNvbTB2MBAGByqGSM49AgEGBSuBBAAiA2IABD/xTNm0QYwn6+2aQRSyCK/a\nEvyvzWwrAHdyXCqdcqXTy8UI2vjDLtMHbxvgSzMBANMGEiKke4LkvYal62M2wXLC\nQOpm6gkC+bGeuphB7nP1sNSk7oAmwBqiEsbSxz0ThTAKBggqhkjOPQQDAgNpADBm\nAjEArYMcvgZJtxY2V6og9XO2WSpIAc0YP4plh0wCFxQyRwLbxscoSKxsmvziiEBx\nHDFFAjEAswbc62BC39emsiGqrRXpcB921h0tJVrQD1OrHBo8zuOabcTgHzhY0LPc\nlGoCR6jQ\n-----END CERTIFICATE-----",
                "key": "$8$LzRR+5tiwtRDLQI2NFQwJ3aVjXDZw8MAmMEvqO/uM9wPHjzq5AEKf8yWMQWIsmspS8GuYWhi\n4UwWBjRnhmuViENZLm5RXjA02Lr42vzHv05skcnnFfCiRL+L8goee8wI+tbI06x4iDnsYhD2\nAAUW1mV8Kb6zAIJ1/AeobAhgY/MvJdVrRpYAY6CWpRQQiCHJbnIsvw82HXqT8fEcKfNeAvLC\nPeLPXJltU89jGlylj899cWUN+CyxTDxko6mvvRaB2MeJSZ5jwnR8bhIubr/hlG1FPlGaOIbm\nP5BYZmhVmFliwQUzlVp+36AxtGG52amLZmudmW5xskOmnhEze5NcbFp8aIF6yUa7AyKE9Rc9\n0kv4W7gNmm2+0YXaMknj1ahTSYESf5sDxN5R6knz0pFf5fF7caun7gmS5Jfqs4OIwVtDjL7J\n2j4rT7hZuwnzIWbUKGu0N9620mWFpF6S9aI2keLzhwYcad1aPMEF6PabEtQPpZMZ9kJVDROe\n5bvf+8pBvNBCtLRCX7+MpKLeFYTzMQ==",
                "passphrase": "$8$4hyAzRD/Wy3WCyocZXv6K4XeM8qDmgfX0CIHtfJYZDY=",
                "verify-client": false,
                "verify-client-depth": 1
            },
            "state": {
                "certificate": "Certificate:\n    Data:\n        Version: 1 (0x0)\n        Serial Number:\n            c9:79:f0:b2:3e:9e:d2:a1\n    Signature Algorithm: ecdsa-with-SHA256\n        Issuer: CN=jim2, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com\n        Validity\n            Not Before: Feb 24 21:35:31 2023 GMT\n            Not After : Feb 24 21:35:31 2024 GMT\n        Subject: CN=jim2, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com\n        Subject Public Key Info:\n            Public Key Algorithm: id-ecPublicKey\n                Public-Key: (384 bit)\n                pub: \n                    04:3f:f1:4c:d9:b4:41:8c:27:eb:ed:9a:41:14:b2:\n                    08:af:da:12:fc:af:cd:6c:2b:00:77:72:5c:2a:9d:\n                    72:a5:d3:cb:c5:08:da:f8:c3:2e:d3:07:6f:1b:e0:\n                    4b:33:01:00:d3:06:12:22:a4:7b:82:e4:bd:86:a5:\n                    eb:63:36:c1:72:c2:40:ea:66:ea:09:02:f9:b1:9e:\n                    ba:98:41:ee:73:f5:b0:d4:a4:ee:80:26:c0:1a:a2:\n                    12:c6:d2:c7:3d:13:85\n                ASN1 OID: secp384r1\n                NIST CURVE: P-384\n    Signature Algorithm: ecdsa-with-SHA256\n         30:66:02:31:00:ad:83:1c:be:06:49:b7:16:36:57:aa:20:f5:\n         73:b6:59:2a:48:01:cd:18:3f:8a:65:87:4c:02:17:14:32:47:\n         02:db:c6:c7:28:48:ac:6c:9a:fc:e2:88:40:71:1c:31:45:02:\n         31:00:b3:06:dc:eb:60:42:df:d7:a6:b2:21:aa:ad:15:e9:70:\n         1f:76:d6:1d:2d:25:5a:d0:0f:53:ab:1c:1a:3c:ce:e3:9a:6d:\n         c4:e0:1f:38:58:d0:b3:dc:94:6a:02:47:a8:d0\n",
                "verify-client": false,
                "verify-client-depth": 1
            }
        }
    }


If you would like to create a self-signed certificate, key, and add a passphrase via the API, you can issue the following API POST command.

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa/f5-openconfig-aaa-tls:tls/create-self-signed-cert

In the body of the API call enter the following JSON syntax.

.. code-block:: json

    {
        "f5-openconfig-aaa-tls:key-type": "encrypted-rsa",
        "f5-openconfig-aaa-tls:key-size": 4096,
        "f5-openconfig-aaa-tls:days-valid": 365,
        "f5-openconfig-aaa-tls:key-passphrase": "Pa$$W0rd!23",
        "f5-openconfig-aaa-tls:confirm-key-passphrase": "Pa$$W0rd!23",
        "f5-openconfig-aaa-tls:name": "velos-1-gsa.cpt.f5net.com",
        "f5-openconfig-aaa-tls:organization": "f5",
        "f5-openconfig-aaa-tls:unit": "sales",
        "f5-openconfig-aaa-tls:city": "boston",
        "f5-openconfig-aaa-tls:region": "ma",
        "f5-openconfig-aaa-tls:country": "us",
        "f5-openconfig-aaa-tls:email": "jim@f5.com",
        "f5-openconfig-aaa-tls:san": "IP:172.22.50.1",
        "f5-openconfig-aaa-tls:version": 1,
        "f5-openconfig-aaa-tls:store-tls": "true"
    }


If you would like to upload a certificate, key, and passphrase you can issue the following API PUT command.

.. code-block:: bash

    PUT https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa/f5-openconfig-aaa-tls:tls

In the body of the API call enter the following JSON syntax.

.. code-block:: json

    {
        "f5-openconfig-aaa-tls:tls": {
            "config": {
                "certificate": "-----BEGIN CERTIFICATE-----\nMIICEjCCAZcCCQDJefCyPp7SoTAKBggqhkjOPQQDAjByMQ0wCwYDVQQDDARqaW0y\nMQswCQYDVQQGEwJVUzELMAkGA1UECAwCTUExDzANBgNVBAcMBkJvc3RvbjELMAkG\nA1UECgwCRjUxDjAMBgNVBAsMBVNhbGVzMRkwFwYJKoZIhvcNAQkBFgpqaW1AZjUu\nY29tMB4XDTIzMDIyNDIxMzUzMVoXDTI0MDIyNDIxMzUzMVowcjENMAsGA1UEAwwE\namltMjELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAk1BMQ8wDQYDVQQHDAZCb3N0b24x\nCzAJBgNVBAoMAkY1MQ4wDAYDVQQLDAVTYWxlczEZMBcGCSqGSIb3DQEJARYKamlt\nQGY1LmNvbTB2MBAGByqGSM49AgEGBSuBBAAiA2IABD/xTNm0QYwn6+2aQRSyCK/a\nEvyvzWwrAHdyXCqdcqXTy8UI2vjDLtMHbxvgSzMBANMGEiKke4LkvYal62M2wXLC\nQOpm6gkC+bGeuphB7nP1sNSk7oAmwBqiEsbSxz0ThTAKBggqhkjOPQQDAgNpADBm\nAjEArYMcvgZJtxY2V6og9XO2WSpIAc0YP4plh0wCFxQyRwLbxscoSKxsmvziiEBx\nHDFFAjEAswbc62BC39emsiGqrRXpcB921h0tJVrQD1OrHBo8zuOabcTgHzhY0LPc\nlGoCR6jQ\n-----END CERTIFICATE-----",
                "key": "$8$LzRR+5tiwtRDLQI2NFQwJ3aVjXDZw8MAmMEvqO/uM9wPHjzq5AEKf8yWMQWIsmspS8GuYWhi\n4UwWBjRnhmuViENZLm5RXjA02Lr42vzHv05skcnnFfCiRL+L8goee8wI+tbI06x4iDnsYhD2\nAAUW1mV8Kb6zAIJ1/AeobAhgY/MvJdVrRpYAY6CWpRQQiCHJbnIsvw82HXqT8fEcKfNeAvLC\nPeLPXJltU89jGlylj899cWUN+CyxTDxko6mvvRaB2MeJSZ5jwnR8bhIubr/hlG1FPlGaOIbm\nP5BYZmhVmFliwQUzlVp+36AxtGG52amLZmudmW5xskOmnhEze5NcbFp8aIF6yUa7AyKE9Rc9\n0kv4W7gNmm2+0YXaMknj1ahTSYESf5sDxN5R6knz0pFf5fF7caun7gmS5Jfqs4OIwVtDjL7J\n2j4rT7hZuwnzIWbUKGu0N9620mWFpF6S9aI2keLzhwYcad1aPMEF6PabEtQPpZMZ9kJVDROe\n5bvf+8pBvNBCtLRCX7+MpKLeFYTzMQ==",
                "passphrase": "$8$4hyAzRD/Wy3WCyocZXv6K4XeM8qDmgfX0CIHtfJYZDY=",
                "verify-client": false,
                "verify-client-depth": 1
            }
        }
    }



Encrypt Management TLS Private Key
=======================

Previously, F5OS allowed an admin to import a TLS certificate and key in clear text. In F5OS-C 1.6.0 an admin can now optionally enter a passphrase with the encrypted private key. This is like the BIG-IP functionality defined in the link below.

`K14912: Adding and removing encryption from private SSL keys (11.x - 16.x) <https://my.f5.com/manage/s/article/K14912>`_


Appliance Mode for F5OS
=======================

If you would like to prevent root / bash level access to the F5OS layer, you can enable **Appliance Mode**, which operates in a similar manner as TMOS appliance mode. Both the F5OS-C system controller and chassis partition layers have a setting where appliance mode can be enabled. Enabling Appliance mode will disable the root account, and access to the underlying bash shell is disabled. The admin account to the F5OS CLI is still enabled. This is viewed as a more secure setting as many vulnerabilities can be avoided by not allowing access to the bash shell. In some heavily audited environments, this setting may be mandatory, but it may prevent lower level debugging from occurring directly in the bash shell. It can be disabled on a temporary basis to do advanced troubleshooting, and then re-enabled when finished.

Enabling Appliance Mode via the CLI
-----------------------------------

Appliance mode can be enabled or disabled via the CLI using the command **system appliance-mode config** and entering either **enabled** or **disabled**. The command **show system appliance-mode** will display the current status. Be sure to commit any changes. 

.. code-block:: bash

    syscon-2-active(config)# system appliance-mode config enabled 
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)# 

To display the current status.

.. code-block:: bash

    syscon-2-active# show system appliance-mode 
    system appliance-mode state enabled
    syscon-2-active#

If you then try to login as root, you will get a permission denied error. You can still login as admin to gain access to the F5OS CLI.

To disable appliance mode.

.. code-block:: bash

    syscon-2-active(config)# system appliance-mode config disabled 
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)#

Enabling Appliance Mode via the webUI
------------------------------------- 

Appliance mode can be enabled or disabled via the webUI under the **System Settings -> General** page in both the system controller webUI and the chassis partition webUI.

.. image:: images/velos_security/image4.png
  :align: center
  :scale: 70%


Enabling Appliance Mode via the API
-----------------------------------

Appliance mode can be enabled or disabled via the API. To view the current status of appliance mode, use the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-appliance-mode:appliance-mode


You will see output like the response below showing the config and state of appliance mode for F5OS.

.. code-block:: json

    {
        "f5-security-appliance-mode:appliance-mode": {
            "config": {
                "enabled": false
            },
            "state": {
                "enabled": false
            }
        }
    }

To change the mode from disabled to enabled, use the following API call.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-appliance-mode:appliance-mode/f5-security-appliance-mode:config

In the body of the API call add the following:

.. code-block:: json

    {
        "f5-security-appliance-mode:config": {
            "f5-security-appliance-mode:enabled": "true"
        }
    }



Appliance Mode for BIG-IP Tenants
=================================

If you would like to prevent root / bash level access to the BIG-IP tenants, you can enable **Appliance Mode** in the tenant settings. Enabling Appliance mode will disable the root account, and access to the underlying bash shell is disabled for BIG-IP. The admin account to the TMOS CLI is still enabled. This is viewed as a more secure setting as many vulnerabilities can be avoided by not allowing access to the bash shell. In some heavily audited environments, this setting may be mandatory, but it may prevent lower level debugging from occurring directly in the bash shell. It can be disabled on a temporary basis to do advanced troubleshooting, and then re-enabled when finished.

Enabling BIG-IP Tenant Appliance Mode via the CLI
--------------------------------------------------

When creating a BIG-IP tenant via the CLI you have the option of enabling or disabling (default) appliance-mode as seen below. 

.. code-block:: bash

    green-partition-chassis1-gsa-1(config-tenant-tenant2)# config ?
            Possible completions:
            appliance-mode           Appliance mode can be enabled/disabled at tenant level
            cryptos                  Enable crypto devices for the tenant.
            dag-ipv6-prefix-length   Tenant default value of IPv6 networking mask used by disaggregator algorithms
            gateway                  User-specified gateway for the tenant static mgmt-ip.
            image                    User-specified image for tenant.
            mac-data                 
            memory                   User-specified memory in MBs for the tenant.
            mgmt-ip                  User-specified mgmt-ip for the tenant management access.
            mgmt-vlan                Mgmt-vlan for tenant mgmt.
            nodes                    User-specified node-number(s) in the partition to schedule the tenant.
            prefix-length            User-specified prefix-length for the tenant static mgmt-ip.
            running-state            User-specified desired state for the tenant.
            storage                  User-specified storage information
            tenant-auth-support      Security can be enabled/disabled when tenant is Not in deployed state.
            type                     Tenant type.
            vcpu-cores-per-node      User-specified number of logical cpu cores for the tenant.
            virtual-wires            User-specified virtual-wires from virtual-wire table for the tenant.
            vlans                    User-specified vlan-id from vlan table for the tenant.
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config cryptos enabled 
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config vcpu-cores-per-node 4
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config type BIG-IP 
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config nodes 2
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config vlans 444        
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config vlans 500
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config vlans 555
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config storage size 76
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config running-state deployed
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config memory 14848
        green-partition-chassis1-gsa-1(config-tenant-tenant2)# config appliance-mode enabled

Any changes must be committed for them to be executed:

.. code-block:: bash

    green-partition-chassis1-gsa-1(config-tenant-tenant2)# commit
    Commit complete.
    green-partition-chassis1-gsa-1(config-tenant-tenant2)# 
	
You may alternatively put all the parameters on one line instead of using the interactive mode above:

.. code-block:: bash

    green-partition-chassis1-gsa-1(config)# tenants tenant tenant2 config image BIGIP-17.1.1.4-0.0.9.ALL-F5OS.qcow2.zip.bundle vcpu-cores-per-node 2 nodes [ 1 ] vlans [ 500 501 ] mgmt-ip 172.22.50.26 prefix-length 26 gateway 172.22.50.62 running-state deployed appliance-mode enabled
    green-partition-chassis1-gsa-1(config-tenant-tenant2)# commit
    Commit complete.
    green-partition-chassis1-gsa-1(config-tenant-tenant2)#


Enabling BIG-IP Tenant Appliance Mode via the webUI
--------------------------------------------

When creating a BIG-IP tenant via the webUI you have the option of enabling or disabling (default) appliance-mode as seen below. 

.. image:: images/velos_security/appliance-mode.png
  :align: center
  :scale: 70%

Enabling BIG-IP Tenant Appliance Mode via the API
------------------------------------------

When creating a BIG-IP tenant via the API you have the option of enabling or disabling (default) appliance-mode as seen below. Tenant creation via the API is as simple as defining the parameters below and sending the POST to the rSeries out-of-band IP address. The API call below will create a tenant; many of the fields are defined as variables in Postman. That way the API calls don't have to be rewritten for different tenant names or IP addressing, or images, and they can be reused easily and adapted to any environment. 

.. code-block:: bash

  POST https://{{velos_chassis1_chassis_partition1_ip}}}:8888/restconf/data/f5-tenants:tenants


Below is the body of the API call above.

.. code-block:: json


    {
        "tenant": [
            {
                "name": "{{New_Tenant1_Name}}",
                "config": {
                    "image": "{{Appliance_Tenant_Image}}",
                    "nodes": [
                        1
                    ],
                    "mgmt-ip": "{{Appliance1_Tenant1_IP}}",
                    "gateway": "{{OutofBand_DFGW}}",
                    "prefix-length": 24,
                    "vlans": [
                        3010,
                        3011,
                        500
                    ],
                    "vcpu-cores-per-node": 2,
                    "memory": 7680,
                    "cryptos": "enabled",
                    "running-state": "configured"
                    "appliance-mode": "enabled"
                }
            }
        ]
    }

Validating Tenant Status via API
================================

The command below will show the current state and status of the tenant. Remember it has not been changed to the **Deployed** state yet.

.. code-block:: bash

  GET https://{{velos_chassis1_chassis_partition1_ip}}}:8888/restconf/data/f5-tenants:tenants

The output of the above API call shows the state and status of the tenant.

.. code-block:: json

    {
        "f5-tenants:tenants": {
            "tenant": [
                {
                    "name": "tenant1",
                    "config": {
                        "name": "tenant1",
                        "type": "BIG-IP",
                        "image": "BIGIP-15.1.5-0.0.8.ALL-F5OS.qcow2.zip.bundle",
                        "nodes": [
                            1
                        ],
                        "mgmt-ip": "10.255.0.149",
                        "prefix-length": 24,
                        "gateway": "10.255.0.1",
                        "vlans": [
                            500,
                            3010,
                            3011
                        ],
                        "cryptos": "enabled",
                        "vcpu-cores-per-node": 2,
                        "memory": "7680",
                        "storage": {
                            "size": 76
                        },
                        "running-state": "configured",
                        "appliance-mode": {
                            "enabled": true
                        }
                    },
                    "state": {
                        "name": "tenant1",
                        "unit-key-hash": "ec+5rtpwnIt6awtkadYqXyWzJ/Oty4tRbfPICaz6OzPSw4KILtQMJZETeq/Q6pbfBh8zXQfBPTetgvPw2dW2ig==",
                        "type": "BIG-IP",
                        "mgmt-ip": "10.255.0.149",
                        "prefix-length": 24,
                        "gateway": "10.255.0.1",
                        "mac-ndi-set": [
                            {
                                "ndi": "default",
                                "mac": "00:94:a1:69:59:24"
                            }
                        ],
                        "vlans": [
                            500,
                            3010,
                            3011
                        ],
                        "cryptos": "enabled",
                        "vcpu-cores-per-node": 2,
                        "memory": "7680",
                        "storage": {
                            "size": 76
                        },
                        "running-state": "configured",
                        "mac-data": {
                            "base-mac": "00:94:a1:69:59:26",
                            "mac-pool-size": 1
                        },
                        "appliance-mode": {
                            "enabled": false
                        },
                        "status": "Configured"
                    }
                }
            ]
        }
    }


Resource Admin & Guest User Role
========================

An earlier F5OS release introduced the **Resource Admin** user role, which is similar to the Admin user role but it cannot create additional local user accounts, delete existing local users, change local user authorizations, or change the set of remotely authenticated users allowed to access the system. Below is an example creating a resource admin user via the CLI. When assigning a new user to role **resource-admin**, their access will be restricted as noted above.

F5OS-C 1.8.0 also adds a new "Guest" role called **user**. The new **user** role available at the F5OS-C system level restricts access to the logs similar to BIG-IP Guest user. F5OS has implemented a new role called **user** which provides read-only access to view all the non-sensitive information on the system. The user role cannot modify any system configurations; however users can change account passwords.


Resource Admin & Guest User Role via CLI
--------------------------------

Below is an example of setting up a new user with the built-in resource-admin role.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication users user res-admin-user config username res-admin-user role resource-admin
    velos-1-gsa-1-active(config-user-res-admin-user)# commit
    Commit complete.
    velos-1-gsa-1-active(config-user-res-admin-user)# config set-password 
    Value for 'password' (<string>): ********
    response Password successfully updated.
    velos-1-gsa-1-active(config-user-res-admin-user)#

When logging in as the resource-admin user, the **aaa** and **aaa authentication** options in the CLI will be limited compared to a normal admin user. The CLI output below shows the full configuration options available to a typical admin user.


.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa ?
    Possible completions:
    authentication    Top-level container for authentication settings.
    password-policy   Top-level container for password-policy settings.
    primary-key       
    restconf-token    restconf-token.
    server-groups     Top-level container for server-group settings.
    tls               Top-level container for key/certificate settings.
    velos-1-gsa-1-active(config)# system aaa

Below is a typical output of **system aaa authentication** for an **admin** role.

.. code-block:: bash    
    
    velos-1-gsa-1-active(config)# system aaa authentication ?
    Possible completions:
    config   
    ldap     Top-level container for LDAP search settings.
    ocsp     Top-level container for OCSP server configurations.
    radius   Top-level container for RADIUS settings.
    roles    Enclosing container list of roles.
    users    Enclosing container list of local users.
    velos-1-gsa-1-active(config)# system aaa authentication


The output below shows the limited **aaa** and **aaa authentication** options available to the resource-admin user. Note, that this role is unable to configure new users, edit users, change password policies, configure the primary-key, server-groups, or rest-conf token timeouts.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa ?
    Possible completions:
    authentication   Top-level container for authentication settings.
    primary-key      
    restconf-token   restconf-token.
    velos-1-gsa-1-active(config)# system aaa

Below is a limited output of **system aaa authentication** for the **resource-admin** role.

.. code-block:: bash    
    
    velos-1-gsa-1-active(config)# system aaa authentication ?
    Possible completions:
    radius   Top-level container for RADIUS settings.
    users    Enclosing container list of local users.
    velos-1-gsa-1-active(config)# system aaa authentication

Below is an example of setting up a new user with the built-in **user** role.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication users user guest-user2 config username guest-user2 role user 
    velos-1-gsa-1-active(config-user-guest-user2)# commit
    Commit complete.
    velos-1-gsa-1-active(config-user-guest-user2)# config set-password 
    Value for 'password' (<string>): ********
    response Password successfully updated.
    velos-1-gsa-1-active(config-user-guest-user2)#


When logging in as the user with the **user** role assigned, the configuration mode will be unavailable. The **user** role will prevent the user from entering config mode.

.. code-block:: bash

    velos-1-gsa-1-active# config
    ----------------------^
    syntax error: expecting 
    autowizard           - Automatically query for mandatory elements
    clear                - Clear parameter
    commit               - Confirm a pending commit
    complete-on-space    - Enable/disable completion on spac

The **user** role will also prevent the user from running **file** operations from the CLI.

.. code-block:: bash

    velos-1-gsa-1-active# file ?
                        ^
    % Invalid input detected at '^' marker.
    velos-1-gsa-1-active# file

Resource Admin & Guest User Role via webUI
--------------------------------

The webUI also supports the assignment of the **resource-admin** role to any user.

.. image:: images/velos_security/imageres-admin.png
  :align: center
  :scale: 70%

When logging in as the resource-admin user, any attempt to configure the restricted items above will result in an **Access Denied** error like the one below.

.. image:: images/velos_security/imageaccessdenied.png
  :align: center
  :scale: 70%

The webUI also supports the assignment of the **user** role to any user.

.. image:: images/velos_security/guest-user.png
  :align: center
  :scale: 70%  

When a user logs in with the **user** role assigned, they can view configuration, but the webUI will prevent any changes from being made by blocking save functions.

.. image:: images/velos_security/guest-user-restricted.png
  :align: center
  :scale: 70%  


Resource-Admin & Guest User Role via API
----------------------------------------

The API also supports the assignment of the resource-admin role to any user.

To view the current user roles:

.. code-block:: bash

    GET https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/openconfig-system:system/aaa/authentication

The output will look similar to the response below. Note, the **resource-admin** role.

.. code-block:: bash


    {
        "openconfig-system:authentication": {
            "config": {
                "f5-aaa-confd-restconf-token:basic": {
                    "enabled": true
                },
                "f5-openconfig-aaa-clientcert:cert-auth": {
                    "enabled": false
                },
                "f5-openconfig-aaa-superuser:superuser-bash-access": false
            },
            "state": {
                "f5-aaa-confd-restconf-token:basic": {
                    "enabled": true
                },
                "f5-openconfig-aaa-clientcert:cert-auth": {
                    "enabled": false
                },
                "f5-openconfig-aaa-superuser:superuser-bash-access": false
            },
            "f5-aaa-confd-restconf-token:state": {
                "basic": {
                    "enabled": true
                }
            },
            "f5-openconfig-aaa-clientcert:clientcert": {
                "config": {
                    "client-cert-name-field": "subjectname-cn",
                    "OID": "UPN"
                },
                "state": {
                    "client-cert-name-field": "subjectname-cn",
                    "OID": "UPN"
                }
            },
            "f5-openconfig-aaa-ldap:ldap": {
                "bind_timelimit": 10,
                "timelimit": 0,
                "idle_timelimit": 0,
                "ldap_version": 3,
                "ssl": "off",
                "active_directory": false,
                "unix_attributes": true,
                "tls_reqcert": "demand",
                "chase-referrals": true
            },
            "f5-openconfig-aaa-ocsp:ocsp": {
                "config": {
                    "override-responder": "off",
                    "response-max-age": -1,
                    "response-time-skew": 300,
                    "nonce-request": "on",
                    "enabled": false
                },
                "state": {
                    "override-responder": "off",
                    "response-max-age": -1,
                    "response-time-skew": 300,
                    "nonce-request": "on",
                    "enabled": false
                }
            },
            "f5-openconfig-aaa-radius:radius": {
                "require_message_authenticator": false
            },
            "f5-system-aaa:users": {
                "user": [
                    {
                        "username": "admin",
                        "config": {
                            "username": "admin",
                            "last-change": "2021-09-29",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "admin",
                            "expiry-status": "enabled"
                        },
                        "state": {
                            "authorized-keys": "-",
                            "username": "admin",
                            "last-change": "2021-09-29",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "admin",
                            "expiry-status": "enabled"
                        }
                    },
                    {
                        "username": "operator",
                        "config": {
                            "username": "operator",
                            "last-change": "2024-04-09",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "operator",
                            "expiry-status": "enabled"
                        },
                        "state": {
                            "authorized-keys": "-",
                            "username": "operator",
                            "last-change": "2024-04-09",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "operator",
                            "expiry-status": "enabled"
                        }
                    },
                    {
                        "username": "root",
                        "config": {
                            "username": "root",
                            "last-change": "2021-11-29",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "root",
                            "expiry-status": "enabled"
                        },
                        "state": {
                            "username": "root",
                            "last-change": "2021-11-29",
                            "tally-count": 0,
                            "expiry-date": "-1",
                            "role": "root",
                            "expiry-status": "enabled"
                        }
                    }
                ]
            },
            "f5-system-aaa:roles": {
                "role": [
                    {
                        "rolename": "admin",
                        "config": {
                            "rolename": "admin",
                            "gid": 9000,
                            "description": "Unrestricted read/write access."
                        },
                        "state": {
                            "rolename": "admin",
                            "gid": 9000,
                            "remote-gid": "-",
                            "ldap-group": "-",
                            "description": "Unrestricted read/write access."
                        }
                    },
                    {
                        "rolename": "operator",
                        "config": {
                            "rolename": "operator",
                            "gid": 9001,
                            "description": "Read-only access to system level data."
                        },
                        "state": {
                            "rolename": "operator",
                            "gid": 9001,
                            "remote-gid": "-",
                            "ldap-group": "-",
                            "description": "Read-only access to system level data."
                        }
                    },
                    {
                        "rolename": "resource-admin",
                        "config": {
                            "rolename": "resource-admin",
                            "gid": 9003,
                            "description": "Restricted read/write access. No access to modify authentication configuration."
                        },
                        "state": {
                            "rolename": "resource-admin",
                            "gid": 9003,
                            "remote-gid": "-",
                            "ldap-group": "-",
                            "description": "Restricted read/write access. No access to modify authentication configuration."
                        }
                    },
                    {
                        "rolename": "superuser",
                        "config": {
                            "rolename": "superuser",
                            "gid": 9004,
                            "description": "Sudo privileges and Bash access to the system (if enabled)."
                        },
                        "state": {
                            "rolename": "superuser",
                            "gid": 9004,
                            "remote-gid": "-",
                            "ldap-group": "-",
                            "description": "Sudo privileges and Bash access to the system (if enabled)."
                        }
                    },
                    {
                        "rolename": "user",
                        "config": {
                            "rolename": "user",
                            "gid": 9002,
                            "description": "Read-only access to non-sensitive system level data."
                        },
                        "state": {
                            "rolename": "user",
                            "gid": 9002,
                            "remote-gid": "-",
                            "ldap-group": "-",
                            "description": "Read-only access to non-sensitive system level data."
                        }
                    }
                ]
            }
        }
    }

To see the current user accounts on the system.

.. code-block:: bash

    GET https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/openconfig-system:system/aaa/authentication/f5-system-aaa:users

The response will detail all the configured user accounts on the system.

.. code-block:: bash


    {
        "f5-system-aaa:users": {
            "user": [
                {
                    "username": "admin",
                    "config": {
                        "username": "admin",
                        "last-change": 19769,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "admin",
                        "expiry-status": "enabled"
                    },
                    "state": {
                        "authorized-keys": "-",
                        "username": "admin",
                        "last-change": 19769,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "admin",
                        "expiry-status": "enabled"
                    }
                },
                {
                    "username": "guest-user2",
                    "config": {
                        "username": "guest-user2",
                        "last-change": 20150,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "user",
                        "expiry-status": "enabled"
                    },
                    "state": {
                        "authorized-keys": "-",
                        "username": "guest-user2",
                        "last-change": 20150,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "user",
                        "expiry-status": "enabled"
                    }
                },
                {
                    "username": "res-admin-user",
                    "config": {
                        "username": "res-admin-user",
                        "last-change": 20150,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "resource-admin",
                        "expiry-status": "enabled"
                    },
                    "state": {
                        "authorized-keys": "-",
                        "username": "res-admin-user",
                        "last-change": 20150,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "resource-admin",
                        "expiry-status": "enabled"
                    }
                },
                {
                    "username": "root",
                    "config": {
                        "username": "root",
                        "last-change": 19825,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "root",
                        "expiry-status": "enabled"
                    },
                    "state": {
                        "username": "root",
                        "last-change": 19825,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "root",
                        "expiry-status": "enabled"
                    }
                },
                {
                    "username": "testuser",
                    "config": {
                        "username": "testuser",
                        "last-change": 19592,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "admin",
                        "expiry-status": "enabled"
                    },
                    "state": {
                        "authorized-keys": "-",
                        "username": "testuser",
                        "last-change": 19592,
                        "tally-count": 0,
                        "expiry-date": "-1",
                        "role": "admin",
                        "expiry-status": "enabled"
                    }
                }
            ]
        }
    }


To create a new user and assign it to the **resource-admin** role, use the following API call.

.. code-block:: bash
    
    PATCH https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/openconfig-system:system/aaa


In the body of the API call add the username and role as seen below.

.. code-block:: bash

    {
    "openconfig-system:aaa": {
        "authentication": {
            "f5-system-aaa:users": {
                "user": [
                    {
                        "username": "resource-admin-user",
                        "config": {
                            "role": "resource-admin"
                        }
                    }
                ]
            }
        }
    }


To create a new user and assign it to the **user** role, use the following API call.

.. code-block:: bash
    
    PATCH https://{{velos_chassis1_chassis_partition1_ip}}:8888/restconf/data/openconfig-system:system/aaa


In the body of the API call add the username and role as seen below.

.. code-block:: bash

    {
    "openconfig-system:aaa": {
        "authentication": {
            "f5-system-aaa:users": {
                "user": [
                    {
                        "username": "guest-user",
                        "config": {
                            "role": "user"
                        }
                    }
                ]
            }
        }
    }



Superuser Role
===============

F5OS-C 1.8.0 adds a new role called **superuser**. The new **superuser** role available at the F5OS-C system level provides **sudo** privileges and bash access to the system (if enabled). This role is intended for environments where appliance mode (prevent bash level access) is disabled, and it only applies to the system controller level in VELOS. Some customers prefer to manage BIG-IP from the bash shell and leverage tmsh commands to pipe into various Unix utilities to parse output. A similar feature has been added to F5OS 1.8.0 where F5OS commands can now be executed from the bash shell via the new f5sh utility. This new role provides a way for a user with "sudo" privileges to be able to be remotely authenticated into the F5OS bash shell but also provides an audit trail of the users interactions with the new f5sh utility in bash shell. 

RBAC on F5OS has been implemented in a way where **Roles** provide slices of privileges that can be composed with each other. There are **Primary Roles** and **Secondary Roles** which can be combined together to give a particular user multiple privileges. 

Users must be assigned to a single primary group/role and can become members of further supplementary groups/roles by adding them to the users list for that group/role.
The roles can be combined together to give a particular user multiple privileges. The **superuser** role is intended to be assigned as a supplementary role in addition to another role like **admin**, whether the role is primary or supplementary does not matter (order does not matter), if only the superuser role was applied it would restrict access to services like the webUI, granting the admin role as a supplemental role will provide normal webUI access.

As an example, assigning a Primary Role of **admin** to a user and then adding that same user to the  **superuser** role will give the user access to the webUI via the admin privileges, and if the **system aaa authentication config superuser-bash-access true** command is set (to true) the default CLI login for this user will be the bash shell. The superuser role does not grant webUI access or Confd CLI access on its own. 


Superuser Role via CLI using Named Groups on LDAP/Active Directory
-----------------------------------------------------------------


To enable LDAP remote authentication, see an example configuration below.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication config authentication-method LDAP_ALL
    velos-1-gsa-1-active(config)# system aaa authentication ldap base distinguishedName=CN=ABC-ADCAdmins,OU=Groups,OU=XYZ,DC=abc123,DC=root,DC=org 
    velos-1-gsa-1-active(config)# system aaa server-groups server-group ldap-group config name ldap-group type LDAP 
    velos-1-gsa-1-active(config-server-group-ldap-group)# servers server 10.10.10.223 config address 10.10.10.223 
    velos-1-gsa-1-active(config-server-10.10.10.223)# ldap config auth-port 389 type ldap 
    velos-1-gsa-1-active(config-server-10.10.10.223)# 

If the LDAP server is an Active Directory server, then the following CLI command should be added.

.. code-block:: bash

    velos-1-gsa-1-active(config)#  system aaa authentication ldap active_directory true
    velos-1-gsa-1-active(config)#  commit
    Commit complete.
    velos-1-gsa-1-active(config)# 

The admin will then need to enable the ldap-group filters for both the primary and supplementary groups/roles which in this case are admin and superuser. In this case, named LADP groups are being used.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication roles role admin config ldap-group <filter for remote admin group>
    velos-1-gsa-1-active(config)# system aaa authentication roles role superuser config ldap-group <filter for remote superuser group>

The ldap-group mapping using the group's LDAP distinguished name is only necessary if the user/group records do not contain "posix/unix attributes" ('gidNumber') that identify the Linux GID of the group. If the records on the remote authentication server have Unix attributes, you can use 'system aaa authentication roles role <role> config remote-gid' to specify the remote group by GID, rather than mapping by name.  

Because this particular configuration is using named LDAP groups, you must disable the **unix_attributes** via the following CLI command. You cannot mix named LDAP groups with GID based unix groups, you must pick one or the other. In this example we are using the named LDAP groups.

.. code-block:: bash

    velos-1-gsa-1-active(config)#  system aaa authentication ldap unix_attributes false
    velos-1-gsa-1-active(config)#  commit
    Commit complete.
    velos-1-gsa-1-active(config)#

If the configuration were using LDAP Group ID's instead of named LDAP groups, then the above configuration would be set to **true**. The configuration above should be enough to remotely authenticate users who are within one or more of the groups specified. To finalize the superuser configuration, you must also set the following F5OS command to **true** to enable bash shell access for users assigned to the superuser group. 

.. code-block:: bash


    velos-1-gsa-1-active(config)#  system aaa authentication config superuser-bash-access true
    velos-1-gsa-1-active(config)#  commit
    Commit complete.
    velos-1-gsa-1-active(config)# 


You can view the current state of these parameters via the following CLI show commands. 

.. code-block:: bash

    velos-1-gsa-1-active# show system aaa authentication 
    system aaa authentication state basic enabled
    system aaa authentication state cert-auth disabled
    system aaa authentication state superuser-bash-access false
    system aaa authentication f5-aaa-token:state basic enabled
    system aaa authentication ocsp state override-responder off
    system aaa authentication ocsp state response-max-age -1
    system aaa authentication ocsp state response-time-skew 300
    system aaa authentication ocsp state nonce-request on
    system aaa authentication ocsp state disabled
                    AUTHORIZED  LAST    TALLY                  EXPIRY   
    USERNAME        KEYS        CHANGE  COUNT  ROLE            STATUS   
    --------------------------------------------------------------------
    admin           -           19769   0      admin           enabled  
    guest-user2     -           20150   0      user            enabled  
    res-admin-user  -           20150   0      resource-admin  enabled  
    root            -           19825   0      root            enabled  
    student1        -           19996   0      admin           enabled  
    student10       -           19996   0      admin           enabled  
    student11       -           19996   0      admin           enabled  
    student12       -           19996   0      admin           enabled  
    student2        -           19996   0      admin           enabled  
    student3        -           19996   0      admin           enabled  
    student4        -           19996   0      admin           enabled  
    student5        -           19996   0      admin           enabled  
    student6        -           19996   0      admin           enabled  
    student7        -           19996   0      admin           enabled  
    student8        -           19996   0      admin           enabled  
    student9        -           19996   0      admin           enabled  
    testuser        -           19592   0      admin           enabled  

                        REMOTE  LDAP                                                                                           
    ROLENAME        GID   GID     GROUP  DESCRIPTION                                                                      USERS  
    -----------------------------------------------------------------------------------------------------------------------------
    admin           9000  -       -      Unrestricted read/write access.                                                  -      
    operator        9001  -       -      Read-only access to system level data.                                           -      
    partition_1     9101  -       -      Provides console access for partition-1.                                         -      
    partition_2     9102  -       -      Provides console access for partition-2.                                         -      
    partition_3     9103  -       -      Provides console access for partition-3.                                         -      
    partition_4     9104  -       -      Provides console access for partition-4.                                         -      
    partition_5     9105  -       -      Provides console access for partition-5.                                         -      
    partition_6     9106  -       -      Provides console access for partition-6.                                         -      
    partition_7     9107  -       -      Provides console access for partition-7.                                         -      
    partition_8     9108  -       -      Provides console access for partition-8.                                         -      
    resource-admin  9003  -       -      Restricted read/write access. No access to modify authentication configuration.  -      
    superuser       9004  -       -      Sudo privileges and Bash access to the system (if enabled).                      -      
    ts_admin        9100  -       -      Provides admin access to the terminal server (TS).                               -      
    user            9002  -       -      Read-only access to non-sensitive system level data.                             -      

    velos-1-gsa-1-active# 


Superuser Role via WebUI using Named Groups on LDAP/Active Directory
---------------------------------------------------------------------

Within the WebUI you can map the superuser role to a remote LDAP group. Go to the **Authentication and Access -> Users & Roles** page and then select **Roles**. Then click on the **superuser** role to edit it.

.. image:: images/velos_security/super-user.png
  :align: center
  :scale: 70%  

Here you can map the superuser role either to a UNIX GID or an LDAP Remote Group, it does not support the configuration of both, so you must pick one method.


.. image:: images/velos_security/super-user-config.png
  :align: center
  :scale: 70% 

If you choose to use the LDAP Group mapping, then you must disable the unix attributes setting in the **Common LDAP Configuration** section.


.. image:: images/velos_security/unix-attributes.png
  :align: center
  :scale: 70% 


Superuser Role via API using Named Groups on LDAP/Active Directory
------------------------------------------------------------------

Coming Soon!

Session Timeouts, Token Lifetime, and Deny Root over SSH
=========================================================

The F5OS CLI timeout is configured under system settings and is controlled via the **idle-timeout** option. This will logout idle sessions to the F5OS CLI whether they are logged in from the console or over SSH.

A new **sshd-idle-timeout** option has been added that will control idle-timeouts for both root sessions to the bash shell over SSH, as well as F5OS CLI sessions over SSH. When the idle-timeout and sshd-idle-timeout are both configured, the shorter interval should take precedence. As an example, if the idle-timeout is configured for three minutes, but the sshd-idle-timeout is set to 2 minutes, then an idle connection that is connected over SSH will disconnect in two minutes, which is the shorter of the two configured options. An idle connection to the F5OS CLI over the console will disconnect in three minutes, because the sshd-idle-timeout doesn't apply to console sessions. 

There is one case that is not covered by either of the above idle-timeout settings until version F5OS-C 1.8.0. When connecting over the console to the bash shell as root, neither of these settings will disconnect an idle session in previous releases. Only console connections to the F5OS CLI are covered via the idle-timeout setting. In F5OS-C 1.8.0 the new **deny-root-ssh** mode when enabled restricts root access over SSH. However, root users can still access the system through the system’s console interface as long as appliance-mode is disabled. If appliance-mode is enabled it overrides this setting, and no root access is allowed via SSH or console. The table below provides more details on the behavior of the setting in conjunction with the appliance mode setting.

+-----------------------------------------------------------+
|                Appliance-mode = Disabled                  |
+================+======================+===================+
| deny-root-ssh  | root console access  | root ssh access   |
+----------------+----------------------+-------------------+
| enabled        | Yes                  | No                |
+----------------+----------------------+-------------------+
| disabled       | Yes                  | Yes               |
+----------------+----------------------+-------------------+


+-----------------------------------------------------------+
|                Appliance-mode = Enabled                   |
+================+======================+===================+
| deny-root-ssh  | root console access  | root ssh access   |
+----------------+----------------------+-------------------+
| enabled        | No                   | No                |
+----------------+----------------------+-------------------+
| disabled       | No                   | No                |
+----------------+----------------------+-------------------+


For the webUI, a token-based timeout is now configurable under the **system aaa** settings. The default RESTCONF token lifetime is 15 minutes and can be configured for a maximum of 1440 minutes. RESTCONF token will be automatically renewed when the token’s lifetime is less than one-third of its original token lifetime. For example, if we set the token lifetime to two minutes, it will be renewed and a new token will be generated, when the token’s lifetime is less than one-third of its original lifetime, that is, anytime between 80 to 120 seconds. However, if a new RESTCONF request is not received within the buffer time (80 to 120 seconds), the token will expire, and you will be logged out of the session. The RESTCONF token will be renewed up to five times, after that the token will not be renewed and you will need to log back in to the system.

Configuring SSH and CLI Timeouts & Deny Root SSH Settings via CLI
----------------------------------------------------------------


To configure the F5OS CLI timeout via the CLI, use the command **system settings config idle-timeout <value-in-seconds>**. Be sure to issue a commit to save the changes. In the case below, a CLI session to the F5OS CLI should disconnect after 300 seconds of inactivity. This will apply to connections to the F5OS CLI over both console and SSH.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system settings config idle-timeout 300
    velos-1-gsa-1-active(config)# commit
    Commit complete.     

To configure the SSH timeout via the CLI, use the command **system settings config sshd-idle-timeout <value-in-seconds>**. This idle-timeout will apply to both bash sessions over SSH, as well as F5OS CLI sessions over SSH. Be sure to issue a commit to save the changes. In the case below, the CLI session should disconnect after 300 seconds of inactivity.


.. code-block:: bash

    velos-1-gsa-1-active(config)# system settings config sshd-idle-timeout 300
    velos-1-gsa-1-active(config)# commit
    Commit complete.      

To configure the deny-root-ssh option use the command **system security config deny-ssh-root**.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security config deny-root-ssh enabled
    velos-1-gsa-1-active(config)# commit
    Commit complete.

Both timeout settings can be viewed using the **show system settings** command.

.. code-block:: bash

    velos-1-gsa-1-active# show system settings 
    system settings state idle-timeout 300
    system settings state sshd-idle-timeout 300
    system settings gui advisory state disabled
    velos-1-gsa-1-active# 

The deny-root-ssh setting can be seen by issuing the CLI command **show system security**.

.. code-block:: bash

    velos-1-gsa-1-active# show system security 
    system security firewall state logging disabled
    system security state deny-root-ssh enabled
    system security services service httpd
    state ssl-ciphersuite ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA
    system security services service sshd
    state ciphers [ aes128-cbc aes128-ctr aes128-gcm@openssh.com aes256-cbc aes256-ctr aes256-gcm@openssh.com ]
    state kexalgorithms [ diffie-hellman-group14-sha1 diffie-hellman-group14-sha256 diffie-hellman-group16-sha512 ecdh-sha2-nistp256 ecdh-sha2-nistp384 ecdh-sha2-nistp521 ]
    velos-1-gsa-1-active# 

 
Configuring SSH and CLI Timeouts & Deny Root SSH Settings via API
-----------------------------------------------------------------

To configure the CLI or SSH timeouts via the API, use the PATCH API call below. In the case below, the CLI session should disconnect after 300 seconds of inactivity.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-settings:settings

Below is the payload in the API call above to set the idle-timeout.

.. code-block:: json

    {
        "f5-system-settings:settings": {
            "f5-system-settings:config": {
                "f5-system-settings:idle-timeout": 300
            }
        }
    }

To view the current idle-timeout settings, issue the following GET API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-settings:settings/config


You'll see output similar to the example below.

.. code-block:: json

    {
        "f5-system-settings:config": {
            "idle-timeout": "40",
            "sshd-idle-timeout": "20"
        }
    }

To configure the deny-root-ssh option, enter the following PATCH API call.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-ciphers:security/f5-security-ciphers:config

In the body of the API call alter the enabled setting to true or false.

.. code-block:: json

    {
        "f5-security-ciphers:config": {
            "deny-root-ssh": {
                "enabled": true
            }
        }
    }


To view the current deny-root-ssh option over the API, issue the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-ciphers:security/f5-security-ciphers:config

The output should look similar to the example below.

.. code-block:: json

    {
        "f5-security-ciphers:config": {
            "deny-root-ssh": {
                "enabled": false
            }
        }
    }


Configuring SSH and CLI Timeouts & Deny Root SSH Settings via webUI
-------------------------------------------------------------------



The CLI timeout and deny-root-ssh settings are both configurable in the webUI. SSH timeouts are not currently configurable via the webUI. The deny-root-ssh and CLI timeout options can be configured in the **System Settings -> System Security** page.

.. image:: images/velos_security/cli-timeout.png
  :align: center
  :scale: 70%


Token Lifetime via CLI
----------------------

As mentioned in the introduction, the webUI and API use token-based authentication and the timeout is based on five token refreshes failing, so the value is essentially five times the configured token lifetime. Use the command **system aaa restconf-token config lifetime <value-in-minutes>** to set the token lifetime. You may configure the restconf-token lifetime via the CLI. The value is in minutes, and the client can refresh the token five times before it expires. As an example, if the restconf-token lifetime is set to 1 minute, an inactive webUI session will have a token expire after one minute, but it can be refreshed a maximum of five times. This will result in a webUI session or API timing out after 5 minutes.

.. code-block:: bash

    syscon-2-active(config)# system aaa restconf-token config lifetime 1
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)#

To display the current restconf-token lifetime setting, use the command **show system aaa**.

.. code-block:: bash

    syscon-2-active# show system aaa
    system aaa restconf-token state lifetime 1
    system aaa primary-key state hash sj2GslitH9XYbmW/cpY0TJhMWkU+CpvAU9vqoiL4aZcfE6qnSUDU3PWx+lCZO5KrqVzlWu/3mRugCNniNyQhSA==
    system aaa primary-key state status NONE
    system aaa authentication f5-aaa-token:state basic enabled
    system aaa authentication f5-aaa-clientcert:state cert-auth disabled
    system aaa authentication ocsp state override-responder off
    system aaa authentication ocsp state response-max-age -1
    system aaa authentication ocsp state response-time-skew 300
    system aaa authentication ocsp state nonce-request on
    system aaa authentication ocsp state disabled
            AUTHORIZED  LAST    TALLY  EXPIRY         
    USERNAME  KEYS        CHANGE  COUNT  DATE    ROLE   
    ----------------------------------------------------
    admin     -           19384   0      -1      admin  
    root      -           19384   0      -1      root   

                        REMOTE         
    ROLENAME        GID   GID     USERS  
    -------------------------------------
    admin           9000  -       -      
    operator        9001  -       -      
    partition_1     9101  -       -      
    partition_2     9102  -       -      
    partition_3     9103  -       -      
    partition_4     9104  -       -      
    partition_5     9105  -       -      
    partition_6     9106  -       -      
    partition_7     9107  -       -      
    partition_8     9108  -       -      
    resource-admin  9003  -       -      
    ts_admin        9100  -       -      
    user            9002  -       -      

    system aaa tls state certificate Certificate:
                                        Data:
                                            Version: 1 (0x0)
                                            Serial Number:
                                                b9:0d:2d:10:75:4a:53:2f
                                        Signature Algorithm: ecdsa-with-SHA256
                                            Issuer: CN=jim, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Validity
                                                Not Before: May  4 16:55:58 2023 GMT
                                                Not After : May  3 16:55:58 2024 GMT
                                            Subject: CN=jim, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Subject Public Key Info:
                                                Public Key Algorithm: id-ecPublicKey
                                                    Public-Key: (384 bit)
                                                    pub: 
                                                        04:59:b3:b5:63:e5:10:93:25:05:1b:55:ef:7a:f8:
                                                        ec:2a:68:19:8a:a9:3e:d3:2f:d4:6c:37:6b:05:5a:
                                                        d3:bc:73:fc:de:d9:fa:58:c5:5e:f2:1d:21:52:3d:
                                                        08:70:e3:47:e3:99:bc:47:6f:c3:b4:04:7b:58:21:
                                                        63:f4:73:33:d3:82:71:c0:ea:5e:82:0f:84:36:ab:
                                                        3d:2f:be:9f:10:ff:05:ad:32:b1:28:fe:52:9b:2c:
                                                        10:ca:29:ee:05:82:f3
                                                    ASN1 OID: secp384r1
                                                    NIST CURVE: P-384
                                        Signature Algorithm: ecdsa-with-SHA256
                                            30:64:02:30:58:3a:be:8d:9e:e0:53:89:12:f2:10:b6:0b:f2:
                                            77:15:cb:eb:7d:55:31:01:70:4e:83:fc:89:f5:f5:e4:1a:4e:
                                            43:81:20:07:4a:0d:e3:72:3a:3e:7c:cb:54:67:b0:1a:02:30:
                                            1c:fe:7c:f1:a5:00:93:77:f2:02:af:82:fc:22:67:ea:35:e7:
                                            0e:9c:b8:90:13:f5:f8:98:f6:07:fe:f9:4b:66:99:32:e9:eb:
                                            92:3d:d2:a2:26:67:c9:01:f9:43:20:a6
                                    
    system aaa tls state verify-client false
    system aaa tls state verify-client-depth 1
    syscon-2-active# 


Token Lifetime via webUI
------------------------

You may configure the restconf-token lifetime via the webUI (new feature added in F5OS-A 1.4.0). The value is in minutes, and the client can refresh the token five times before it expires. As an example, if the token lifetime is set to 1 minute, an inactive webUI session will have a token expire after one minute, but it can be refreshed a maximum of five times. This will result in the webUI session timing out after 5 minutes. The HTTPS Token Lifetime is configurable under the **Authentication & Access -> Authentication Settings** page.

.. image:: images/velos_security/image6.png
  :align: center
  :scale: 70%

Token Lifetime via API
----------------------

You may configure the restconf-token lifetime via the API. The value is in minutes, and the client can refresh the token five times before it expires. As an example, if the token lifetime is set to 1 minute, an inactive webUI session or API session will have a token expire after one minute, but it can be refreshed a maximum of five times. This will result in the webUI session timing out after 5 minutes.

Use the following API PATCH call to set the restconf-token lifetime, or any other password policy parameter.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa

In the body of the API call adjust the restconf-token lifetime setting to the desired timeout in minutes. The example below is 10 minutes, and the session will timeout at five times the value of the lifetime setting due to token refresh.

.. code-block:: json

    {
        "openconfig-system:aaa": {
            "authentication": {
                "config": {
                    "f5-aaa-confd-restconf-token:basic": {
                        "enabled": true
                    }
                }
            },
            "f5-aaa-confd-restconf-token:restconf-token": {
                "config": {
                    "lifetime": 10
                }
            },
            "f5-openconfig-aaa-password-policy:password-policy": {
                "config": {
                    "min-length": 6,
                    "required-numeric": 0,
                    "required-uppercase": 0,
                    "required-lowercase": 0,
                    "required-special": 0,
                    "required-differences": 8,
                    "reject-username": false,
                    "apply-to-root": true,
                    "retries": 3,
                    "max-login-failures": 10,
                    "unlock-time": 60,
                    "root-lockout": true,
                    "root-unlock-time": 60,
                    "max-age": 0
                }
            }
        }
    }


Disabling Basic Authentication
==============================

F5OS utilizes basic authentication (username/password) as well as token-based authentication for both the API and the webUI. Generally, username/password is issued by the client to obtain a token from F5OS, which is then used to make further inquiries or changes. Tokens have a relatively short lifetime for security reasons, and the user is allowed to refresh that token a certain number of times before they are forced to re-authenticate using basic authentication again. Although token-based authentication is supported, basic authentication can still be utilized to access F5OS and make changes by default. A new option was added to allow basic authentication to be disabled, except for the means of obtaining a token. Once a token is issued to a client, it will be the only way to make changes via the webUI or the API. 


Disabling Basic Auth via the CLI
--------------------------------

The default setting for basic auth is enabled, and the current state can be seen by entering the **show system aaa** command. The line **system aaa authentication state basic enabled** indicates that basic authentication is still enabled. 

.. code-block:: bash

    velos-1-gsa-1-active# show system aaa
    system aaa restconf-token state lifetime 5
    system aaa primary-key state hash sUwBWJYT/VCne4xBVIdSfmjylG7QjMUskI1gtAKIfHifeahm/3/Ywq8zSdV2wn+RsiHdG+3EM/Ilih9GXQoyMA==
    system aaa primary-key state status "COMPLETE        Initiated: Thu May 30 19:22:13 2024"
    system aaa authentication state basic enabled
    system aaa authentication state cert-auth disabled
    system aaa authentication state superuser-bash-access false
    system aaa authentication f5-aaa-token:state basic enabled
    system aaa authentication ocsp state override-responder off
    system aaa authentication ocsp state response-max-age -1
    system aaa authentication ocsp state response-time-skew 300
    system aaa authentication ocsp state nonce-request on
    system aaa authentication ocsp state disabled
                    AUTHORIZED  LAST    TALLY                  EXPIRY   
    USERNAME        KEYS        CHANGE  COUNT  ROLE            STATUS   
    --------------------------------------------------------------------
    admin           -           19769   0      admin           enabled  
    guest-user2     -           20150   0      user            enabled  
    res-admin-user  -           20150   0      resource-admin  enabled  
    root            -           19825   0      root            enabled  
    student1        -           19996   0      admin           enabled  
    student10       -           19996   0      admin           enabled  
    student11       -           19996   0      admin           enabled  
    student12       -           19996   0      admin           enabled  
    student2        -           19996   0      admin           enabled  
    student3        -           19996   0      admin           enabled  
    student4        -           19996   0      admin           enabled  
    student5        -           19996   0      admin           enabled  
    student6        -           19996   0      admin           enabled  
    student7        -           19996   0      admin           enabled  
    student8        -           19996   0      admin           enabled  
    student9        -           19996   0      admin           enabled  
    testuser        -           19592   0      admin           enabled  

                        REMOTE  LDAP                                                                                           
    ROLENAME        GID   GID     GROUP  DESCRIPTION                                                                      USERS  
    -----------------------------------------------------------------------------------------------------------------------------
    admin           9000  -       -      Unrestricted read/write access.                                                  -      
    operator        9001  -       -      Read-only access to system level data.                                           -      
    partition_1     9101  -       -      Provides console access for partition-1.                                         -      
    partition_2     9102  -       -      Provides console access for partition-2.                                         -      
    partition_3     9103  -       -      Provides console access for partition-3.                                         -      
    partition_4     9104  -       -      Provides console access for partition-4.                                         -      
    partition_5     9105  -       -      Provides console access for partition-5.                                         -      
    partition_6     9106  -       -      Provides console access for partition-6.                                         -      
    partition_7     9107  -       -      Provides console access for partition-7.                                         -      
    partition_8     9108  -       -      Provides console access for partition-8.                                         -      
    resource-admin  9003  -       -      Restricted read/write access. No access to modify authentication configuration.  -      
    superuser       9004  -       -      Sudo privileges and Bash access to the system (if enabled).                      -      
    ts_admin        9100  -       -      Provides admin access to the terminal server (TS).                               -      
    user            9002  -       -      Read-only access to non-sensitive system level data.                             -      

    NAME           TYPE  ADDRESS        PORT  
    ------------------------------------------
    diags-ad-test  LDAP  10.144.75.137  636   

    system aaa tls state certificate Certificate:
                                        Data:
                                            Version: 1 (0x0)
                                            Serial Number:
                                                d9:d2:0b:3a:93:85:54:9d
                                        Signature Algorithm: sha256WithRSAEncryption
                                            Issuer: CN=prod-partition.f5demo.net, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Validity
                                                Not Before: Mar 18 20:32:40 2024 GMT
                                                Not After : Feb 16 20:32:40 2026 GMT
                                            Subject: CN=prod-partition.f5demo.net, C=US, ST=MA, L=Boston, O=F5, OU=Sales/emailAddress=jim@f5.com
                                            Subject Public Key Info:
                                                Public Key Algorithm: rsaEncryption
                                                    Public-Key: (4096 bit)
                                                    Modulus:
                                                        00:bd:c3:13:03:14:23:c5:8d:5c:6d:1b:2f:ed:88:
                                                        88:95:1b:49:2f:1d:40:e2:ce:d0:68:ab:77:30:c0:
                                                        e6:28:8c:5b:20:f1:25:6b:62:4f:5f:3d:e1:b8:1f:
                                                        90:38:68:d6:12:52:9b:04:81:23:d5:72:95:a6:61:
                                                        e3:a2:5f:5e:9a:45:a4:c1:df:ac:7c:91:68:90:8a:
                                                        49:e4:95:7b:d4:26:fc:af:75:fc:15:dc:12:8b:42:
                                                        ca:af:48:cd:e0:c6:27:17:93:ed:26:9a:eb:5e:3f:
                                                        07:72:33:27:cb:89:6e:19:ab:6e:fd:e2:d5:6e:cc:
                                                        42:9b:7e:0f:e0:ed:56:37:cf:68:c4:45:23:44:f9:
                                                        0c:9e:24:4f:ca:3a:9b:dd:64:bd:13:3d:36:e3:c1:
                                                        f1:5a:60:00:a4:ec:08:87:6d:14:8e:d6:c4:00:43:
                                                        a4:3c:48:24:b7:fc:e4:4f:14:fb:81:c7:8f:cc:8e:
                                                        47:8b:93:f4:9d:06:09:49:22:22:d0:98:b8:5b:ba:
                                                        c5:08:52:a9:db:a6:55:b1:42:c1:2c:19:6e:10:e7:
                                                        1b:73:46:c5:06:56:51:3a:95:d8:5a:ec:9a:44:94:
                                                        89:41:3c:14:1c:96:1c:88:7c:47:41:91:8b:53:cf:
                                                        48:01:82:be:8f:a0:cf:f9:7d:59:52:3a:b6:c1:1f:
                                                        83:8e:dc:f1:cd:7c:22:53:0c:fb:0d:1f:a1:fd:bd:
                                                        24:c4:0a:69:d5:23:07:36:04:fe:36:75:0b:7a:7c:
                                                        56:11:95:94:98:84:a6:dd:25:47:ae:03:22:8b:c8:
                                                        9e:a4:f0:29:4c:e4:ff:e4:37:4e:e9:b2:8d:f0:72:
                                                        b5:c3:b7:c9:3e:08:9f:3b:e8:4b:47:fd:e4:24:25:
                                                        18:a6:6b:5e:4a:36:ea:56:7e:6d:51:05:5a:dc:29:
                                                        e2:7b:b4:ee:0c:ae:8d:fa:60:97:13:c5:ad:24:b3:
                                                        c5:58:27:f6:87:d1:be:68:0b:0a:07:4c:e0:b5:5b:
                                                        fe:3d:49:fe:9b:b4:49:06:a2:09:82:7f:68:a6:c5:
                                                        a1:f2:39:7f:4a:21:37:2e:92:94:1c:c6:c9:7f:c9:
                                                        8a:4c:d7:d4:dc:81:7a:0c:e1:89:03:ca:d0:4e:93:
                                                        ac:2e:12:aa:99:26:d7:08:b8:09:e9:4f:20:99:75:
                                                        53:d9:f6:30:d8:df:88:98:0e:29:c8:95:90:8a:a8:
                                                        c6:e6:f7:33:ae:7f:c0:80:3b:d0:56:8f:f0:67:1e:
                                                        1e:ab:59:be:4c:05:15:1a:f4:16:33:e8:ea:7b:dc:
                                                        36:13:12:d8:b7:43:5b:3b:4f:78:74:b6:dc:ae:6e:
                                                        23:20:4b:09:f5:79:0d:46:1c:99:82:97:6b:7a:1c:
                                                        06:f4:6b
                                                    Exponent: 65537 (0x10001)
                                        Signature Algorithm: sha256WithRSAEncryption
                                            a0:e7:47:0f:21:eb:b9:4e:cc:88:0f:4d:3c:1e:d6:41:21:ea:
                                            65:a5:92:3b:99:f9:e2:f1:ff:ec:8e:ac:a2:9f:da:ad:4e:fd:
                                            ea:b2:df:6b:18:f9:a6:86:9c:bd:41:66:01:c9:dc:76:e0:55:
                                            25:76:17:07:fe:6f:d3:84:94:c3:23:a1:72:fb:37:62:1e:05:
                                            eb:f0:4c:22:e6:90:28:07:69:0d:c4:0c:68:c4:06:60:dd:d8:
                                            82:59:0c:eb:f6:0e:7a:35:a1:0f:3b:1d:01:a4:05:5d:cc:dc:
                                            73:b3:26:e1:1f:52:b9:2b:86:9a:ef:8e:c6:ac:73:d2:60:ac:
                                            cd:b7:0e:af:07:30:ae:f5:4e:3a:df:9e:09:5b:58:5a:5a:9d:
                                            f9:6b:14:78:5c:fc:00:67:bf:a3:4c:27:a7:ce:55:b5:4d:32:
                                            dd:4d:10:7c:00:72:9c:aa:e6:94:fa:24:8f:36:d5:d3:d2:16:
                                            2a:2b:d0:3e:3f:22:27:ed:03:8c:89:66:f9:9e:ce:fa:23:87:
                                            ac:04:db:18:8d:be:27:aa:0a:5a:76:47:ca:12:b7:aa:9c:5e:
                                            11:68:85:fe:f5:48:75:26:87:68:23:e7:4b:11:aa:a0:75:11:
                                            56:b5:58:63:5c:4d:ba:b7:4b:54:d9:07:ec:2c:76:38:29:6b:
                                            63:38:a3:14:8f:af:65:6f:89:58:7a:d0:c9:89:72:65:d4:64:
                                            23:9a:cf:eb:e0:1f:5f:90:04:44:22:fe:d7:8e:e8:5d:c2:38:
                                            f1:d9:6b:de:d8:b7:e8:2c:fb:28:0f:5d:4e:c4:34:b5:5c:b9:
                                            13:54:49:13:c3:07:f5:f7:43:b5:49:5a:d4:04:81:30:70:64:
                                            86:68:0c:e7:d7:81:57:ac:27:56:e2:3b:df:6a:09:5c:19:04:
                                            c8:97:58:15:be:8b:4b:00:18:51:50:7d:84:0e:7a:c0:94:0b:
                                            5a:e0:fc:72:12:e0:de:14:7d:3d:19:6c:d3:73:03:93:a1:ab:
                                            b1:d6:36:44:22:d4:71:b6:89:e7:69:45:b1:d0:16:d6:c7:fa:
                                            3d:27:4d:e0:99:86:94:75:84:96:2d:1d:78:14:43:cd:09:6d:
                                            89:44:cb:99:93:86:23:1a:68:e9:49:b4:53:c9:18:05:2a:18:
                                            58:36:1e:14:6f:88:56:43:a3:9a:e9:b0:f5:cf:f6:6c:21:22:
                                            9b:db:d2:57:67:82:ee:0e:f7:e5:a9:af:f2:e1:44:fa:53:b9:
                                            45:14:9d:2a:43:d3:a7:ec:28:fe:bb:e2:a2:98:4e:8f:27:25:
                                            46:b3:2e:e0:be:21:4e:6c:51:cb:40:22:7d:19:1f:5b:7a:6e:
                                            c5:64:11:29:83:02:86:f5
                                    
    system aaa tls state verify-client false
    system aaa tls state verify-client-depth 1
    velos-1-gsa-1-active# 


You may disable basic authentication by issuing the cli command **system aaa authentication config basic disabled**, and then committing the change.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication config basic disabled
    velos-1-gsa-1-active(config)# commit
    Commit complete.
    rvelos-1-gsa-1-active(config)#

To re-enable basic authentication, change the state to enabled and commit.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication config basic enabled 
    velos-1-gsa-1-active(config)# commit
    Commit complete.
    velos-1-gsa-1-active(config)#



Disabling Basic Auth via the API
--------------------------------

You may enable or disable basic authentication via the API. The default setting for basic authentication is enabled, and the current state can be seen by entering the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa/authentication/config

You should see the returned output below with the basic authentication state set to either **true** or **false**.

.. code-block:: json

    {`
        "openconfig-system:config": {
            "f5-aaa-confd-restconf-token:basic": {
                "enabled": true
            }
        }
    }

Use the following API PATCH call to set the restconf-token:basic setting to **true** or **false**, or to adjust any other password policy parameter.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa

In the body of the API call adjust the restconf-token:basic setting to **true** or **false**.

.. code-block:: json

    {
        "openconfig-system:aaa": {
            "authentication": {
                "config": {
                    "f5-aaa-confd-restconf-token:basic": {
                        "enabled": true
                    }
                }
            },
            "f5-aaa-confd-restconf-token:restconf-token": {
                "config": {
                    "lifetime": 10
                }
            },
            "f5-openconfig-aaa-password-policy:password-policy": {
                "config": {
                    "min-length": 6,
                    "required-numeric": 0,
                    "required-uppercase": 0,
                    "required-lowercase": 0,
                    "required-special": 0,
                    "required-differences": 8,
                    "reject-username": false,
                    "apply-to-root": true,
                    "retries": 3,
                    "max-login-failures": 10,
                    "unlock-time": 60,
                    "root-lockout": true,
                    "root-unlock-time": 60,
                    "max-age": 0
                }
            }
        }
    }


Disabling Basic Auth via the webUI
----------------------------------

Disabling basic authentication via the webUI is available via the **User Management -> Authentication Settings** page and you'll see a drop-down box to enable or disable **Basic Authentication**.

.. image:: images/velos_security/image5.png
  :align: center
  :scale: 70%

Confirming Basic Auth is Disallowed
-----------------------------------

With basic authentication enabled (default setting), you can make any API call using username/password (basic auth) authentication. Using the Postman utility this can be demonstrated on any configuration change by setting The Auth Type to **Basic Auth** and configuring a username and password as seen below.

.. image:: images/velos_security/imagebasicauth.png
  :align: center
  :scale: 70%

While basic auth is enabled, any API call using username/password will complete successfully. After disabling basic auth, any attempt to access an API endpoint other than the root URI using basic auth will fail with a message similar to the one below indicating **access denied**.

.. code-block:: json

    {
        "ietf-restconf:errors": {
            "error": [
                {
                    "error-type": "application",
                    "error-tag": "access-denied",
                    "error-path": "/openconfig-system:system/aaa",
                    "error-message": "access denied"
                }
            ]
        }
    }

When basic authentication is enabled, a client will be allowed to obtain an auth token using username/password at any URI. The client can then choose to use the auth token for subsequent requests, or they can continue to use basic auth (username/password) authentication. As an example, the curl command below uses basic auth successfully to the URI endpoint **restconf/data/openconfig-system:system/config**. In the response you can see the **X-Auth-Token** header, which contains the auth token that can then be used by the client for subsequent requests:

.. code-block:: bash

    jprompt% curl -i -sku admin:admin -H "Content-Type: application/yang-data+json"  https://172.22.50.9:8888/restconf/data/openconfig-system:system/config
    HTTP/1.1 200 OK
    Date: Mon, 03 Mar 2025 22:46:02 GMT
    Server: Apache
    Referrer-Policy: strict-origin-when-cross-origin
    Last-Modified: Mon, 03 Mar 2025 22:34:32 GMT
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Etag: "1741-41272-839432@master"
    Content-Type: application/yang-data+json
    Pragma: no-cache
    X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzQxMDQxOTYyIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3NDEwNDIyNjIsImlhdCI6MTc0MTA0MTk2MiwicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4yLjEyIn0.u4E0myI64mhszYrvAXX12L3OXW_4abzrim3LOY7Nivw
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
    Transfer-Encoding: chunked

    {
    "openconfig-system:config": {
        "hostname": "velos-1-gsa.floating.cpt.f5net.com",
        "login-banner": "This is the Global Solution Architect's VELOS Chassis1 in the Seattle CPT Lab. Unauthorized use is prohibited. Please reach out to Jim McCarron with any questions.",
        "motd-banner": "Welcome to the GSA VELOS Chassis1 in Seattle."
    }
    }
    prompt%


Here is an example of the client issuing the same request with the auth token it received above to the same endpoint. Instead of specifying a user with the -u option, insert the header **X-Auth-Token** and add the token from the initial response above.

.. code-block:: bash

    prompt% curl -i -sk -H "Content-Type: application/yang-data+json"  -H "X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzQxMDQxOTYyIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3NDEwNDIyNjIsImlhdCI6MTc0MTA0MTk2MiwicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4yLjEyIn0.u4E0myI64mhszYrvAXX12L3OXW_4abzrim3LOY7Nivw" https://172.22.50.9:8888/restconf/data/openconfig-system:system/config
    HTTP/1.1 200 OK
    Date: Mon, 03 Mar 2025 22:52:16 GMT
    Server: Apache
    Referrer-Policy: strict-origin-when-cross-origin
    Last-Modified: Mon, 03 Mar 2025 22:34:32 GMT
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Etag: "1741-41272-839432@master"
    Content-Type: application/yang-data+json
    Pragma: no-cache
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
    Transfer-Encoding: chunked

    {
    "openconfig-system:config": {
        "hostname": "velos-1-gsa.floating.cpt.f5net.com",
        "login-banner": "This is the Global Solution Architect's VELOS Chassis1 in the Seattle CPT Lab. Unauthorized use is prohibited. Please reach out to Jim McCarron with any questions.",
        "motd-banner": "Welcome to the GSA VELOS Chassis1 in Seattle."
    }
    }
    prompt %

If the same exercise is repeated after basic auth is disabled, then the user will not be able to run the initial request using basic auth (username/password). It will fail to any non-root URI as seen below. The response will contain and **access-denied** error.

.. code-block:: bash

    prompt% curl -i -sku admin:admin -H "Content-Type: application/yang-data+json"  https://172.22.50.9:8888/restconf/data/openconfig-system:system/config
    HTTP/1.1 401 Unauthorized
    Date: Mon, 03 Mar 2025 22:54:28 GMT
    Server: Apache
    Referrer-Policy: strict-origin-when-cross-origin
    Content-Length: 144
    Content-Type: application/yang-data+json
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block

    {
    "ietf-restconf:errors": {
        "error": [
        {
            "error-type": "protocol",
            "error-tag": "access-denied"
        }
        ]
    }
    }
    prompt % 


By changing the URI to use the top-level API endpoint: (:8888/restconf) or (:443/api), the client will now be able to obtain a token using basic authentication, but the token will be needed for any other API endpoints.

.. code-block:: bash

    prompt% curl -i -sku admin:admin -H "Content-Type: application/yang-data+json"  https://172.22.50.9:8888/restconf     
    HTTP/1.1 200 OK
    Date: Mon, 03 Mar 2025 22:57:32 GMT
    Server: Apache
    Referrer-Policy: strict-origin-when-cross-origin
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Content-Length: 90
    Content-Type: application/yang-data+json
    Pragma: no-cache
    X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzQxMDQyNjUyIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3NDEwNDI5NTIsImlhdCI6MTc0MTA0MjY1MiwicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4yLjEyIn0.8ZYzM5sW_a3i-cGrkkVcSHDbCxJBkc0PZlea7L3eDcI
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block

    {"ietf-restconf:restconf":{"data":{},"operations":{},"yang-library-version":"2019-01-04"}}                                                                                     
    prompt % 

Setting Password Policies
=========================

You may configure the local password policy to ensure secure passwords are utilized, re-use is minimized, and to limit the amount of failures/retries. Below are some of the settings that can be set.

- **Minimum Password Length** - For Minimum Length, specify the minimum number of characters (6 to 255) required for a valid password.
- **Password Required Characters** - For Required Characters, specify the minimum number of Numeric, Uppercase, Lowercase, and Special characters that are required in a valid password.
- **New/Old Password Differential** - For New/Old Password Differential, specify the number of character changes in the new password that differentiate it from the old password. The default value is 8.
- **Disallow Username** - For Disallow Username, set to True to check whether the name of the user in forward or reversed form is contained in the password. The default value is False.
- **Apply Password Policy to Root Account** - For Apply Password Policy to Root Account, set to True to use the same password policy for the root account. The default value is True.
- **Maximum Password Retries** - For Maximum Password Retries, specify the number of times that a user can try to create an acceptable password. The default value is 3.
- **Maximum Login Attempts** - For Maximum Login Attempts, specify the number of times a user can attempt to log in before the account is temporarily suspended. The default value is 10; 0 means no limit.
- **Lockout Duration** - For Lockout Duration, specify the duration, in seconds, an account is locked out. The default value is 60.
- **Maximum Password Age** - For Max Password Age, specify the number of days after which the password will expire after being changed. 0 means never expires.

Setting Password Policies via CLI
---------------------------------

Local Password Policies can be set in the CLI using the **system aaa password-policy config** command. Adding a question mark after the command will show all the configurable options. Be sure to commit after making any changes.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa password-policy config ?
    Possible completions:
    apply-to-root          Apply password policy to administrators when setting passwords for other user accounts.
    max-age                Number of days after which the user will have to change the password.
    max-class-repeat       Reject passwords with this many repeating upper/lowercase letters, digits or special characters such as '!@#$%' in the password.
    max-letter-repeat      Reject passwords with this many repeating lower-case letters in the password.
    max-login-failures     Number of unsuccessful login attempts allowed before lockout.
    max-sequence-repeat    Reject passwords with this many repeating upper/lowercase letters or digits in the password.
    min-length             Minimum length of a new password.
    reject-username        Reject passwords that contain the username.
    required-differences   Required number of differences between the old and new passwords.
    required-lowercase     Required number of lowercase characters in password.
    required-numeric       Required number of numeric digits in password.
    required-special       Required number of 'special' characters in password.
    required-uppercase     Required number of uppercase character in password.
    retries                Number of times to prompt before failing.
    root-lockout           Enable lockout of root users.
    root-unlock-time       Time (seconds) before the root account is automatically unlocked.
    unlock-time            Time (seconds) before a locked account is automatically unlocked.
    velos-1-gsa-1-active(config)#

Setting Password Policies via webUI
---------------------------------

Local Password Policies can be set in the **User Management -> Authentication Settings** page in the webUI.

.. image:: images/velos_security/passwordpolicy1.png
  :align: center
  :scale: 70%

Setting Password Policies via API
---------------------------------

Local Password Policies can be viewed or set via the API using the following API calls. To view the current password policy settings, issue the following GET API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa/f5-openconfig-aaa-password-policy:password-policy

The JSON output will reflect the current settings.

.. code-block:: json

    {
        "f5-openconfig-aaa-password-policy:password-policy": {
            "config": {
                "min-length": 6,
                "required-numeric": 0,
                "required-uppercase": 0,
                "required-lowercase": 0,
                "required-special": 0,
                "required-differences": 8,
                "reject-username": false,
                "apply-to-root": true,
                "retries": 3,
                "max-login-failures": 10,
                "unlock-time": 60,
                "root-lockout": true,
                "root-unlock-time": 60,
                "max-age": 0
            }
        }
    }

To change any of the password policy parameters, use the following API GET call.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa

In the payload of the API call adjust the appropriate parameters under **f5-openconfig-aaa-password-policy:password-policy**.


.. code-block:: json

    {
        "openconfig-system:aaa": {
            "authentication": {
                "config": {
                    "f5-aaa-confd-restconf-token:basic": {
                        "enabled": true
                    }
                }
            },
            "f5-aaa-confd-restconf-token:restconf-token": {
                "config": {
                    "lifetime": 10
                }
            },
            "f5-openconfig-aaa-password-policy:password-policy": {
                "config": {
                    "min-length": 6,
                    "required-numeric": 0,
                    "required-uppercase": 0,
                    "required-lowercase": 0,
                    "required-special": 0,
                    "required-differences": 8,
                    "reject-username": false,
                    "apply-to-root": true,
                    "retries": 3,
                    "max-login-failures": 10,
                    "unlock-time": 60,
                    "root-lockout": true,
                    "root-unlock-time": 60,
                    "max-age": 0
                }
            }
        }
    }

Remote Authentication
=====================

The F5OS platform layer supports both local and remote authentication. By default, there are local users enabled for both admin and root access. You will be forced to change passwords for both accounts on initial login. Many users will prefer to configure the F5OS layer to use remote authentication via LDAP, RADIUS, AD, or TACACS+. The F5OS TMOS based tenants maintain their own local or remote authentication, and details are covered in standard TMOS documentation.

`Configuring Remote User Authentication and Authorization on TMOS <https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/tmos-implementations-13-0-0/10.html>`_

In earlier versions F5OS only supported static pre-defined roles which in turn map to specific group IDs. Users created and managed on external LDAP, Active Directory, RADIUS, or TACACS+ servers must have the same group IDs on the external authentication servers as they do within F5OS based systems to allow authentication and authorization to occur. Users created on external LDAP, Active Directory, RADIUS, or TACACS+ servers must be associated with one of these group IDs on the system. The supported F5OS static group IDs and the roles they map to are seen in the table below. Newer versions of F5OS-C now support User defined roles.

+----------------+----------+
| Role           | Group ID | 
+================+==========+
| admin          | 9000     | 
+----------------+----------+
| operator       | 9001     |
+----------------+----------+
| tenant-console | 9100     | 
+----------------+----------+

From a high level the **admin** role (group ID 9000) is a read/write role with full access to the system to make changes. The **operator** role (group ID 9001) is a read-only role and is prevented from making any configuration changes. The **root** role (group ID 0) gives full access to the bash shell, and in some environments this role will be disabled by enabling appliance mode. Note that the root role is not allowed access via remote authentication. The last role is **tenant-console** (group ID 9100), and this role is used to provide remote access directly to the tenant console as noted here:

`Console Access to Tenant via Built-In Terminal Server <https://clouddocs.f5.com/training/community/VELOS-training/html/VELOS_diagnostics.html#console-access-via-built-in-terminal-server>`_

The group IDs are typically specified in a user configuration file on the external server (file locations vary on different servers). You can assign these F5 user attributes: 

.. code-block:: bash

    F5-F5OS-UID=1001 

    F5-F5OS-GID=9000   <-- THIS MUST MATCH /etc/group items    

    F5-F5OS-HOMEDIR=/tmp  <-- Optional; prevents sshd warning msgs  

    F5-F5OS-USERINFO=test_user  <-- Optional user info  

    F5-F5OS-SHELL=/bin/bash    <--  Ignored; always set to /var/lib/controller/f5_confd_cli 

Setting F5-F5OS-HOMEDIR=/tmp is a good idea to avoid warning messages from sshd that the directory does not exist. Also, the source address in the TACACS+ configuration is not used by the VELOS system. 

If F5-F5OS-UID is not set, it defaults to 1001. If F5-F5OS-GID is not set, it defaults to 0 (disallowed for authentication). The F5-F5OS-USERINFO is a comment field. Essentially, F5-F5OS-GID is the only hard requirement and must coincide with group ID's user role. 

More specific configuration details can be found in the **User Management** section of the **VELOS System Administration Guide**.

`F5OS User Management <https://techdocs.f5.com/en-us/velos-1-8-0/velos-systems-administration-configuration/title-auth-access.html#user-roles-overview>`_

The **gidNumber** attribute needs to either be on the user or on a group the user is a member of. The **gidNumber** must be one of those listed (9000, 9001, 9100). [The root role is not externally accessible via remote authentication.] 

In older releases, the role numbers (9000, 9001, 9100) are fixed and hard-coded. The implementation relies on AD “unix attributes” being installed into the directory. The role IDs are fixed. As noted above, in newer releases the GIDs are configurable, but this is still based on numeric GIDs not group names. 

A new LDAP group functionality was added in F5OS-C 1.8.0, and this can now be used in place of the GID based implementation above. You should configure one or the other, but not both.

Roles are mutually exclusive. While it is theoretically possible to assign a user to multiple role groups, It is up to the underlying Confd to resolve how the roles present to it are assigned, and it doesn’t always choose the most logical answer. For that reason, you should consider them mutually exclusive and put the user in the role with the least access necessary to do their work. More details, on configuration of F5OS-A 1.3.0 can be found below.

`LDAP/AD configuration overview <https://techdocs.f5.com/en-us/velos-1-8-0/velos-systems-administration-configuration/title-auth-access.html#ldap-config-overview>`_

Changing Group ID Mapping via CLI 
---------------------------------------------------------

Newer F5OS releases have added the ability to customize the Group ID mapping to the remote authentication server. In previous releases the Group IDs were static, now they can be changed to map to user selectable Group IDs. Below is an example of changing the remote Group ID for the admin account to a custom value of 9200.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system aaa authentication roles role admin config remote-gid 9200
    velos-1-gsa-1-active(config-role-admin)# commit
    Commit complete.
    velos-1-gsa-1-active(config-role-admin)# 

To view the current mappings use the **show system aaa authentication roles** CLI command.

.. code-block:: bash

    velos-1-gsa-1-active# show system aaa authentication roles
                        REMOTE  LDAP                                                                                           
    ROLENAME        GID   GID     GROUP  DESCRIPTION                                                                      USERS  
    -----------------------------------------------------------------------------------------------------------------------------
    admin           9000  -       -      Unrestricted read/write access.                                                  -      
    operator        9001  -       -      Read-only access to system level data.                                           -      
    partition_1     9101  -       -      Provides console access for partition-1.                                         -      
    partition_2     9102  -       -      Provides console access for partition-2.                                         -      
    partition_3     9103  -       -      Provides console access for partition-3.                                         -      
    partition_4     9104  -       -      Provides console access for partition-4.                                         -      
    partition_5     9105  -       -      Provides console access for partition-5.                                         -      
    partition_6     9106  -       -      Provides console access for partition-6.                                         -      
    partition_7     9107  -       -      Provides console access for partition-7.                                         -      
    partition_8     9108  -       -      Provides console access for partition-8.                                         -      
    resource-admin  9003  -       -      Restricted read/write access. No access to modify authentication configuration.  -      
    superuser       9004  -       -      Sudo privileges and Bash access to the system (if enabled).                      -      
    ts_admin        9100  -       -      Provides admin access to the terminal server (TS).                               -      
    user            9002  -       -      Read-only access to non-sensitive system level data.                             -      

    velos-1-gsa-1-active# 


Login Banner / Message of the Day
===================

Some environments require warning or acceptance messages to be displayed to clients connecting to the F5OS layer at initial connection time and/or upon successful login. The F5OS layer supports configurable Message of the Day (MoTD) and Login Banners that are displayed to clients connecting to the F5OS layer via both CLI and the webUI. The MoTD and Login Banner can be configured via CLI, webUI, or API. The Login Banner is displayed at initial connect time and is commonly used to notify users they are connecting to a specific resource, and that they should not connect if they are not authorized. The MoTD is displayed after successful login and may also display some information about the resource the user is connecting to.

Configuring Login Banner / MoTD via CLI
---------------------------------------

Enter config mode and use the command **system config login-banner** to configure the login banner via the CLI. You must commit the change afterwards.

.. code-block:: bash

    syscon-2-active(config)# system config login-banner "This is a restricted resource. Unauthorized access is prohibited. Please disconnect now if you are not authorized." 
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)# 

Enter config mode and use the command **system config motd-banner** to configure the Message of the Day banner via the CLI. You must commit the change afterwards.

.. code-block:: bash

    syscon-2-active(config)# system config motd-banner "Welcome to the GSA VELOS Chassis1, do not make any changes to configuration without a ticket." 
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)#

To display both settings, use the **show system state** command.

.. code-block:: bash

    syscon-2-active# show system state 
    system state hostname velos-chassis1.f5demo.net
    system state login-banner This is a restricted resource. Unauthorized access is prohibited. Please disconnect now if you are not authorized.
    system state motd-banner Welcome to the GSA VELOS Chassis1, do not make any changes to configuration without a ticket.
    system state current-datetime "2023-03-29 22:24:29-04:00"
    syscon-2-active# 



Configuring Login Banner / MoTD via webUI
-----------------------------------------

You may configure both the Login Banner and the Message of the Day Banner via the webUI on the **System Settings -> General** page.

.. image:: images/velos_security/image7.png
  :align: center
  :scale: 70%



Configuring Login Banner / MoTD via API
---------------------------------------

You may configure both the Login Banner and the Message of the Day Banner via the API using the following API calls.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system

In the body of the API call configure the desired message of the day and login banner settings.

.. code-block:: json

    {
        "openconfig-system:system": {
            "config": {
                "hostname": "velos-chassis1.f5demo.net",
                "login-banner": "This is the Global Solution Architect's VELOS VELOS Chassis1 in the Boston Lab. Unauthorized use is prohibited. Please reach out to admin with any questions.",
                "motd-banner": "Welcome to the GSA VELOS Chassis 1 in Boston"
            }
        }
    }

To view the currently configured MoTD and login banner, issue the following GET API request.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/config

The output will contain the current MoTD and login banner configuration.

.. code-block:: json

    {
        "openconfig-system:config": {
            "hostname": "velos-chassis1.f5demo.net",
            "login-banner": "This is a restricted resource. Unauthorized access is prohibited. Please disconnect now if you are not authorized.",
            "motd-banner": "Welcome to the GSA VELOS Chassis1, do not make any changes to configuration without a ticket."
        }
    }


Display of Login Banner and MoTD
--------------------------------

Below is an example of the Login Banner being displayed before the user is prompted for a password during an SSH connection to the F5OS platform layer. After a successful user login, the MoTD is then displayed. 

.. code-block:: bash

    prompt$ ssh -l admin 10.255.0.147
    This is a restricted resource. Unauthorized access is prohibited. Please disconnect now if you are not authorized.
    admin@10.255.0.147's password: 
    Last login: Wed Mar 29 22:22:46 2023 from 172.18.105.132
    Welcome to the GSA VELOS Chassis1, do not make any changes to configuration without a ticket.
    System Time: 2023-03-29 22:31:53 EDT
    Welcome to the F5OS System Controller Management CLI
    User admin last logged in 2023-03-30T02:31:52.261266+00:00, to controller-2, from 172.18.105.132 using rest-http
    admin connected from 172.18.105.132 using ssh on syscon-2-active
    syscon-2-active#

Below is an example of the Login Banner being displayed before the user is prompted for a password during a webUI connection to the F5OS platform layer. After a successful user login, the MoTD is then displayed.


.. image:: images/velos_security/image8.png
  :align: center
  :scale: 70%


.. image:: images/velos_security/image9.png
  :align: center
  :scale: 70%  


SNMPv3
=======

F5OS-A 1.2.0 added support for SNMPv3. Earlier versions of F5OS-A only supported SNMPv1/v2c. SNMPv3 provides a more secure monitoring environment through the use of authenticated access. More details can be found here:

`VELOS F5OS-C SNMP Monitoring and Alerting <https://clouddocs.f5.com/training/community/velos-training/html/velos_monitoring_snmp.html>`_


NTP Authentication
==================

NTP Authentication can be enabled to provide a secure communication channel for Network Time Protocol queries from the F5OS platform layer. To utilize NTP authentication you must first enable NTP authentication and then add keys in order to secure communication to your NTP servers.

Enabling NTP Authentication via CLI
-----------------------------------

To enable NTP authentication use the **system ntp config enable-ntp-auth true** command in the CLI and then commit the change.

.. code-block:: bash

    syscon-2-active(config)# system ntp config enable-ntp-auth true 
    syscon-2-active(config)# commit
    Commit complete.
    syscon-2-active(config)# 

Next, you'll need to add keys for NTP Authentication

.. code-block:: bash

    syscon-2-active(config)# system ntp ntp-keys ntp-key 11 config key-id 11 key-type F5_NTP_AUTH_SHA1 key-value HEX:E27611234BB5E7CDFC8A8ACE55B567FC5CA7C890

The key ID, key type, and key value on this client system must match the server exactly. Lastly, you'll need to associate the key with an NTP server using the configured key-id above.

.. code-block:: bash

    syscon-2-active(config)# system ntp servers server 10.255.0.139
    syscon-2-active(config-server-10.255.0.139)# config key-id 11
    syscon-2-active(config-server-10.255.0.139)# 

Enabling NTP Authentication via webUI
-------------------------------------

To enable NTP authentication in the webUI use the **System Settings -> Time Settings** page. You'll need to enable NTP authentication then add the appropriate keys and then associate those keys with an NTP server.

.. image:: images/velos_security/ntpauth1.png
  :align: center
  :scale: 70%  

Enabling NTP Authentication via API
-----------------------------------

NTP authentication can also be set and viewed using the F5OS API. To view the current NTP setting use the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/ntp

The output will display the current NTP configuration state including authentication and keys.

.. code-block:: json

    {
        "openconfig-system:ntp": {
            "config": {
                "enabled": true,
                "enable-ntp-auth": true
            },
            "state": {
                "enabled": true,
                "enable-ntp-auth": true
            },
            "ntp-keys": {
                "ntp-key": [
                    {
                        "key-id": 11,
                        "config": {
                            "key-id": 11,
                            "key-type": "f5-system-ntp:F5_NTP_AUTH_SHA1",
                            "key-value": "$8$IIACWGpGPUYzian06FdH5PpH/sbSNQmre6DVsBZ2zxCv6S5vM3cXUkn8NwD0BABSeT3Drnmm\npLCQibKafAFFPg=="
                        },
                        "state": {
                            "key-id": 11,
                            "key-type": "F5_NTP_AUTH_SHA1",
                            "key-value": "$8$IIACWGpGPUYzian06FdH5PpH/sbSNQmre6DVsBZ2zxCv6S5vM3cXUkn8NwD0BABSeT3Drnmm\npLCQibKafAFFPg=="
                        }
                    }
                ]
            },
            "servers": {
                "server": [
                    {
                        "address": "10.255.0.139",
                        "config": {
                            "address": "10.255.0.139",
                            "port": 123,
                            "version": 4,
                            "association-type": "SERVER",
                            "iburst": false,
                            "prefer": false,
                            "f5-openconfig-system-ntp:key-id": 11
                        },
                        "state": {
                            "address": "10.255.0.139",
                            "port": 123,
                            "version": 4,
                            "association-type": "SERVER",
                            "iburst": false,
                            "prefer": false,
                            "f5-openconfig-system-ntp:key-id": 11,
                            "f5-openconfig-system-ntp:authenticated": false
                        }
                    },
                    {
                        "address": "time.f5net.com",
                        "config": {
                            "address": "time.f5net.com",
                            "port": 123,
                            "version": 4,
                            "association-type": "SERVER",
                            "iburst": false,
                            "prefer": false
                        },
                        "state": {
                            "address": "time.f5net.com",
                            "port": 123,
                            "version": 4,
                            "association-type": "SERVER",
                            "iburst": false,
                            "prefer": false,
                            "f5-openconfig-system-ntp:authenticated": false
                        }
                    }
                ]
            }
        }
    }

To enable NTP authentication via the F5OS API use the following API call.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/ntp

In the body of the API call you can enable NTP authentication, add keys, and associate those keys with an NTP server using the key-id.

.. code-block:: json

    {
        "openconfig-system:ntp": {
            "config": {
                "enabled": true,
                "enable-ntp-auth": true
            },
            "ntp-keys": {
                "ntp-key": [
                    {
                        "key-id": 11,
                        "config": {
                            "key-id": 11,
                            "key-type": "f5-system-ntp:F5_NTP_AUTH_SHA1",
                            "key-value": "$8$IIACWGpGPUYzian06FdH5PpH/sbSNQmre6DVsBZ2zxCv6S5vM3cXUkn8NwD0BABSeT3Drnmm\npLCQibKafAFFPg=="
                        }
                    }
                ]
            },
            "servers": {
                "server": [
                    {
                        "address": "10.255.0.139",
                        "config": {
                            "address": "10.255.0.139",
                            "port": 123,
                            "version": 4,
                            "association-type": "SERVER",
                            "iburst": false,
                            "prefer": false,
                            "f5-openconfig-system-ntp:key-id": 11
                        }
                    }
                ]
            }
        }
    }




Configurable Management Ciphers
===============================

F5OS-C 1.6.0 added the ability to display and configure the ciphers used for the management interface of F5OS. The **show system security** CLI command will display the **ssl-ciphersuite** for the webUI/httpd management interface. It will also display the **ciphers** and **kexalgorithms** for the sshd service. Below is an example of the default settings. 

.. code-block:: bash

    velos-1-gsa-1-active# show system security 
    system security firewall state logging disabled
    system security state deny-root-ssh disabled
    system security services service httpd
    state ssl-ciphersuite ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA
    system security services service sshd
    state ciphers [ aes128-cbc aes128-ctr aes128-gcm@openssh.com aes256-cbc aes256-ctr aes256-gcm@openssh.com ]
    state kexalgorithms [ diffie-hellman-group14-sha1 diffie-hellman-group14-sha256 diffie-hellman-group16-sha512 ecdh-sha2-nistp256 ecdh-sha2-nistp384 ecdh-sha2-nistp521 ]
    velos-1-gsa-1-active# 

You can change the ciphers offered by F5OS to clients connecting to the httpd service by using the **system security services service httpd config ssl-ciphersuite** CLI command and then choosing the ciphers you would like to enable. Be sure to commit any changes.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service httpd config ssl-ciphersuite ?
    Possible completions:
    ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:D
    HE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384
    :ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PS
    K-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES1
    28-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AE
    S128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAM
    ELLIA128-SHA:PSK-AES128-CBC-SHA
    velos-1-gsa-1-active(config)# system security services service httpd config ssl-ciphersuite
    
You can change the ciphers, kexalgorithms, host-key-algorithms, and macs offered by F5OS to clients connecting to the sshd service by using the **system security services service sshd config ?** CLI command, and then choosing the ciphers you would like to enable. Be sure to commit any changes.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service sshd config ?                
    Possible completions:
    ciphers               User specified ciphers.
    host-key-algorithms   User specified hostkeyalgorithms.
    kexalgorithms         User specified kexalgorithms.
    macs                  User specified MACs.
    velos-1-gsa-1-active(config)# system security services service sshd config


Below are the current options for sshd ciphers, kexalgorithms and macs. You may configure which ciphers F5OS will use for the sshd service by using the **system security services service sshd config ciphers** command.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service sshd config ciphers ?
    Description: User specified ciphers.
    Possible completions:
    [  
    [ 3des-cbc blowfish-cbc cast128-cbc arcfour arcfour128 arcfour256 aes128-cbc aes192-cbc aes256-cbc rijndael-cbc@lysator.liu.se aes128-ctr aes192-ctr aes256-ctr aes128-gcm@openssh.
    com aes256-gcm@openssh.com chacha20-poly1305@openssh.com ]
    velos-1-gsa-1-active(config)# system security services service sshd config ciphers

You may configure which kexalgorithms F5OS will use for the sshd service by using the **system security services service sshd config kexalgorithms** command.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service sshd config kexalgorithms ?
    Description: User specified kexalgorithms.
    Possible completions:
    [  
    [ diffie-hellman-group1-sha1 diffie-hellman-group14-sha1 diffie-hellman-group14-sha256 diffie-hellman-group16-sha512 diffie-hellman-group18-sha512 diffie-hellman-group-exchange-sh
    a1 diffie-hellman-group-exchange-sha256 ecdh-sha2-nistp256 ecdh-sha2-nistp384 ecdh-sha2-nistp521 curve25519-sha256 curve25519-sha256@libssh.org gss-gex-sha1- gss-group1-sha1- gss-gr
    oup14-sha1- ]
    velos-1-gsa-1-active(config)# system security services service sshd config kexalgorithms

You may configure which macs F5OS will use for the sshd service by using the **system security services service sshd config macs** command.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service sshd config macs ?        
    Description: User specified MACs.
    Possible completions:
    [  
    [ hmac-sha1 mac-sha1-96 hmac-sha2-512 hmac-sha1 hmac-sha1-96 hmac-sha2-256 hmac-md5 hmac-md5-96 hmac-ripemd160 hmac-ripemd160 hmac-ripemd160@openssh.com umac-64@openssh.com umac-1
    28@openssh.com hmac-sha1-etm@openssh.com hmac-sha1-96-etm@openssh.com hmac-sha2-256-etm@openssh.com hmac-sha2-512-etm@openssh.com hmac-md5-etm@openssh.com hmac-md5-96-etm@openssh.co
    m hmac-ripemd160-etm@openssh.com umac-64-etm@openssh.com umac-128-etm@openssh.com ]
    velos-1-gsa-1-active(config)# system security services service sshd config macs 

You may configure which host-key-algorithms F5OS will use for the sshd service by using the **system security services service sshd config macs** command.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system security services service sshd config host-key-algorithms ?
    Description: User specified hostkeyalgorithms.
    Possible completions:
    [  [ ssh-rsa ]
    velos-1-gsa-1-active(config)# system security services service sshd config host-key-algorithms

Configuring Management Ciphers via webUI
--------------------------------------

You can configure which ciphers are used when connecting to the F5OS management interface using SSH or HTTPS. Go to the **System Settings -> System Security** page to configure both httpd and sshd ciphers suites, sshd KEX algorithms, sshd MAC algorithms, and sshd host key algorithms.

.. image:: images/velos_security/security-ciphers.png
  :align: center
  :scale: 70%  





Configuring Management Ciphers via API
--------------------------------------

You can configure which ciphers are used when connecting to the F5OS management interface using SSH or HTTPS. Use the following API call to configure both httpd and sshd ciphers suites, sshd KEX algorithms, sshd MAC algorithms, and sshd host key algorithms.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-ciphers:security

In the body of the API call enter your httpd and sshd ciphers suites, sshd KEX algorithms, sshd MAC algorithms, and sshd host key algorithms.

.. code-block:: bash

    {
        "f5-security-ciphers:security": {
            "services": {
                "service": [
                    {
                        "name": "httpd",
                        "config": {
                            "name": "httpd",
                            "ssl-ciphersuite": "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA"
                        },
                        "state": {
                            "name": "httpd",
                            "ssl-ciphersuite": "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA"
                        }
                    },
                    {
                        "name": "sshd",
                        "config": {
                            "name": "sshd",
                            "ciphers": [
                                "aes128-cbc",
                                "aes128-ctr",
                                "aes128-gcm@openssh.com",
                                "aes256-cbc",
                                "aes256-ctr",
                                "aes256-gcm@openssh.com"
                            ],
                            "kexalgorithms": [
                                "diffie-hellman-group14-sha1",
                                "diffie-hellman-group14-sha256",
                                "diffie-hellman-group16-sha512",
                                "ecdh-sha2-nistp256",
                                "ecdh-sha2-nistp384",
                                "ecdh-sha2-nistp521"
                            ]
                        }
                    }
                ]
            },
            "firewall": {
                "config": {
                    "logging": {
                        "enabled": false
                    }
                }
            },
            "config": {
                "deny-root-ssh": {
                    "enabled": false
                }
            }
        }
    }

You can then view the current configuration by issuing the following API call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-security-ciphers:security

The output will look similar to the response below.

.. code-block:: bash

    {
        "f5-security-ciphers:security": {
            "services": {
                "service": [
                    {
                        "name": "httpd",
                        "config": {
                            "name": "httpd",
                            "ssl-ciphersuite": "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA"
                        },
                        "state": {
                            "name": "httpd",
                            "ssl-ciphersuite": "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-DSS-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA256:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-CAMELLIA256-SHA:DHE-DSS-CAMELLIA256-SHA:ECDH-RSA-AES256-GCM-SHA384:ECDH-ECDSA-AES256-GCM-SHA384:ECDH-RSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA:ECDH-ECDSA-AES256-SHA:AES256-GCM-SHA384:AES256-SHA256:AES256-SHA:CAMELLIA256-SHA:PSK-AES256-CBC-SHA:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:DHE-DSS-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-SHA256:DHE-DSS-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA:DHE-RSA-CAMELLIA128-SHA:DHE-DSS-CAMELLIA128-SHA:ECDH-RSA-AES128-GCM-SHA256:ECDH-ECDSA-AES128-GCM-SHA256:ECDH-RSA-AES128-SHA256:ECDH-ECDSA-AES128-SHA256:ECDH-RSA-AES128-SHA:ECDH-ECDSA-AES128-SHA:AES128-GCM-SHA256:AES128-SHA256:AES128-SHA:CAMELLIA128-SHA:PSK-AES128-CBC-SHA"
                        }
                    },
                    {
                        "name": "sshd",
                        "config": {
                            "name": "sshd",
                            "ciphers": [
                                "aes128-cbc",
                                "aes128-ctr",
                                "aes128-gcm@openssh.com",
                                "aes256-cbc",
                                "aes256-ctr",
                                "aes256-gcm@openssh.com"
                            ],
                            "kexalgorithms": [
                                "diffie-hellman-group14-sha1",
                                "diffie-hellman-group14-sha256",
                                "diffie-hellman-group16-sha512",
                                "ecdh-sha2-nistp256",
                                "ecdh-sha2-nistp384",
                                "ecdh-sha2-nistp521"
                            ]
                        },
                        "state": {
                            "name": "sshd",
                            "ciphers": [
                                "aes128-cbc",
                                "aes128-ctr",
                                "aes128-gcm@openssh.com",
                                "aes256-cbc",
                                "aes256-ctr",
                                "aes256-gcm@openssh.com"
                            ],
                            "kexalgorithms": [
                                "diffie-hellman-group14-sha1",
                                "diffie-hellman-group14-sha256",
                                "diffie-hellman-group16-sha512",
                                "ecdh-sha2-nistp256",
                                "ecdh-sha2-nistp384",
                                "ecdh-sha2-nistp521"
                            ]
                        }
                    }
                ]
            },
            "firewall": {
                "config": {
                    "logging": {
                        "enabled": false
                    }
                },
                "state": {
                    "logging": {
                        "enabled": false
                    }
                }
            },
            "config": {
                "deny-root-ssh": {
                    "enabled": false
                }
            },
            "state": {
                "deny-root-ssh": {
                    "enabled": false
                }
            }
        }
    }


Client Certificate Based Auth
=============================

You can configure client certificate-based authentication to the F5OS management interfaces.

Configuring Client Certificate Authentication via CLI
-----------------------------------------------------

Before you can log in to the webUI using client certificate authentication, you must have configured client certificate authentication from the CLI and imported the certificate to your browser. 

`SSH public key authentication overview <https://techdocs.f5.com/en-us/velos-1-8-0/velos-systems-administration-configuration/title-auth-access.html#ssh-public-key-auth-overview>'_



Configuring Client Certificate Authentication via webUI
-------------------------------------------------------

Although you can enable client certificate authentication via the webUI, you must upload or create your certificate via the CLI or API first. Otherwise, you will end up being locked out of the webUI, until the full configuraton is completed.

See the section above about configuration of the certificate before moving on. If you have loaded a certificate, then you can enable client certificate authentication via the webUI as seen below.

.. image:: images/velos_security/client-cert1.png
  :align: center
  :scale: 70% 


**More Details to Come**

Configuring Client Certificate Authentication via API
-----------------------------------------------------

Proxy Server Configuration
==========================

F5OS supports the ability to capture detailed logs and configuration using the qkView utility. To speed up support case resolution, the qkView can be uploaded directly to F5's iHealth service, which will give F5 support personnel access to the detailed information to aid problem resolution. In some environments, F5 devices may not have the ability to access the Internet without going through a proxy. F5OS-C has supported the ability to upload qkViews directly to iHealth through a proxy device and F5OS-C 1.8.0 added support for activating a license via proxy.


Proxy Server via CLI for Licensing and Qkview Uploads to iHealth
----------------------------------------------------------------

To add a proxy server via the CLI which can be used for iHealth uploads or license activation, use the **system diagnostics proxy** command.

.. code-block:: bash

    velos-1-gsa-1-active(config)# system diagnostics proxy config proxy-username myusername proxy-server https://myproxy.com:3128 proxy-password 
    (<AES encrypted string>): **************
    velos-1-gsa-1-active(config)#  

In F5OS-C 1.8.0 the system licensing command has been extended to accept proxy configuration details as seen below.

.. code-block:: bash

    velos-1-gsa-1-active(config)#  system licensing install registration-key F9832-03399-18781-56079-7591756 proxy-server https://myproxy.com:3128 proxy-username user1 proxy-password 
    Value for 'proxy-password' (<AES encrypted string>): **************
    result License installed successfully.
    velos-1-gsa-1-active(config)# 

Proxy Server via webUI for Licensing and Qkview Uploads to iHealth
----------------------------------------------------------------

To add a proxy server for iHealth uploads via the webUI, go to the **Diagnostics -> iHealth Configuration** page. 

.. image:: images/velos_security/imageproxy1.png
  :align: center
  :scale: 70%  

To add a proxy server for license activation via the webUI, go to the **System Settings -> Licensing** page. 

.. image:: images/velos_security/proxy-licensing.png
  :align: center
  :scale: 70%  

Proxy Server via API for Licensing and Qkview Uploads to iHealth
----------------------------------------------------------------

To add a proxy server for iHealth uploads via the API, use the following API call.

.. code-block:: bash

    PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-diagnostics-qkview:diagnostics/f5-system-diagnostics-proxy:proxy

In the body of the API call add the username, password, and proxy server configuration.

.. code-block:: json


    {
        "f5-system-diagnostics-proxy:proxy": {
            "config": {
                "proxy-username": "username2",
                "proxy-password": "$8$8FudCujBpUpoTBaQQw4QaTeyUU8UHdkYAv90Dfx43SA=",
                "proxy-server": "https://myproxy2.demo.f5net"
            }
        }
    }


To view the current proxy configuration via the API, use the following call.

.. code-block:: bash

    GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/f5-system-diagnostics-qkview:diagnostics/f5-system-diagnostics-proxy:proxy

The API call should return output similar to what is seen below.

.. code-block:: json

    {
        "f5-system-diagnostics-proxy:proxy": {
            "state": {
                "proxy-username": "username",
                "proxy-server": "https://myproxy.demo.f5net"
            },
            "config": {
                "proxy-username": "username",
                "proxy-password": "$8$8FudCujBpUpoTBaQQw4QaTeyUU8UHdkYAv90Dfx43SA=",
                "proxy-server": "https://myproxy.demo.f5net"
            }
        }
    }


Audit Logging
============

F5OS can log all configuration changes and access to the F5OS layer in audit logs. In versions prior to F5OS-C 1.6.0, all access and configuration changes for the system controller layer are logged in one of two separate **audit.log** files. The files reside in the in one of the following paths in the F5OS filesystem when logged in as root; **/var/F5/controller/log/audit.log** or **/var/log/audit/audit.log**. If you are logged into the F5OS CLI as admin, then the actual paths are simplified to **log/controller/audit.log** and **/log/host/audit/audit.log** for the system controller layer.

In versions prior to F5OS-C 1.6.0, all access and configuration changes for the chassis partition layer are logged in one of two separate **audit.log** files. The files reside in the in one of the following paths in the F5OS filesystem when logged in as root on the system controller; **/var/F5/partition<ID#>/log/audit.log** or **/var/log/audit/audit.log**. If you are logged into the F5OS CLI as admin, then the actual paths are simplified to **log/audit.log** and **/log/host/audit/audit.log** for the system controller layer.

In versions prior to F5OS-C 1.6.0, the audit.log files may only be viewed locally within the F5OS layer, the audit logs cannot be sent to a remote syslog location. F5OS-A 1.6.0 adds the ability to allow audit.log entries to be redirected to a remote syslog location, as well as changing the log format to conform to standard F5OS syslog format of all audit related events. Details on the two different implementations are below.

Viewing Audit Logs via F5OS CLI (F5OS-C 1.6.0 and Later)
--------------------------------------------------------

Any information related to login/logout or configuration changes are logged in the **log/controller/audit.log** location. By default, these events are not sent to a configured remote syslog location. If you would like to send informational audit level messages to a remote syslog server, then you must explicitly enable audit events.

First, you must configure the remote syslog destination. As part of that configuration, you will specify the IP address, port, and protocol of the remote syslog server. To send audit.log events to the remote server you must add the command **selectors selector AUTHPRIV DEBUG** as seen below.

.. code-block:: bash

    syscon-1-active(config)# system logging remote-servers remote-server 10.255.85.164
    syscon-1-active(config-remote-server-10.255.0.139)# config remote-port 514
    syscon-1-active(config-remote-server-10.255.0.139)# config proto udp
    syscon-1-active(config-remote-server-10.255.0.139)# selectors selector LOCAL0 INFORMATIONAL
    syscon-1-active(config-remote-server-10.255.0.139)# selectors selector AUTHPRIV DEBUG
    syscon-1-active(config-remote-server-10.255.0.139)# commit
    Commit complete.
    syscon-1-active(config-remote-server-10.255.0.139)#

Then, you can control the level of events that will be logged to the local audit.log file by configuring the **audit-service** **sw-component**. By default, all audit events will be logged, but you can turn down the level of events

.. code-block:: bash

    syscon-1-active(config)# do show running-config system logging sw-components sw-component audit-service 
    system logging sw-components sw-component audit-service
    config name audit-service
    config description "Audit message handling service"
    config severity INFORMATIONAL
    !

The formatting of audit logs provides the date/time in UTC, the account and ID who performed the action, the type of event, the asset affected, the type of access, and success or failure of the request. Separate log entries provide details on user access (login/login failures) information such as IP address and port and whether access was granted or not.


Viewing Audit Logs via F5OS CLI
-------------------------------

Most audit events go to the **log/controller/audit.log** location, while a few others such as CLI login failures are logged to **log/host/audit/audit.log** in the current F5OS releases. In the F5OS CLI, the paths are simplified so that you don’t have to know the underlying directory structure. You can use the **file list path** command to see the files inside the **log/controller/** directory; use the tab complete to see the options. You may choose either the **log/controller** directory or the **log/host/audit** directory. Note the **audit.log** file. 

.. code-block:: bash

    syscon-1-active# file list path log/
    Possible completions:
    confd/  controller/  host/
    syscon-1-active# file list path log/controller/
    entries {
        name afu-cookie
        date Wed Jul 12 20:22:09 UTC 2023
        size 33B
    }
    entries {
        name audit.log
        date Wed Aug 23 18:38:05 UTC 2023
        size 7.0MB
    }
    entries {
        name audit.log.1
        date Wed Jul 12 19:39:02 UTC 2023
        size 11MB
    }
    entries {
        name audit.log.2.gz
        date Mon May 22 21:23:01 UTC 2023
        size 512KB
    }
    entries {
        name audit.log.3.gz
        date Tue May  2 21:30:59 UTC 2023
        size 498KB
    }
    entries {
        name audit.log.4.gz
        date Fri Apr  7 18:53:58 UTC 2023
        size 512KB
    }
    entries {
        name audit.log.5.gz
        date Thu Apr  6 17:26:46 UTC 2023
        size 507KB
    }
    entries {
        name cc-confd
        date Wed Aug  2 23:17:42 UTC 2023
        size 1.2MB
    }
    entries {
        name cc-confd-hal
        date Wed Jul 12 20:21:45 UTC 2023
        size 0B
    }
    entries {
        name cc-confd-health
        date Wed Aug 23 18:38:03 UTC 2023
        size 74MB
    }
    entries {
        name cc-confd-health-diag-agent
        date Wed Jul 12 20:21:46 UTC 2023
        size 0B
    }
    entries {
        name cc-confd-init
        date Wed Jul 12 20:21:44 UTC 2023
        size 228KB
    }
    entries {
        name cc-upgrade.dbg
        date Wed Jul 12 20:22:04 UTC 2023
        size 149KB
    }
    entries {
        name chassis-manager
        date Sun Aug 13 02:48:15 UTC 2023
        size 76MB
    }
    entries {
        name chassis-manager.1
        date Mon Feb 27 01:46:02 UTC 2023
        size 101MB
    }
    entries {
        name confd
        date Thu Jan 26 22:00:08 UTC 2023
        size 0B
    }
    entries {
        name confd_go_standby
        date Wed Feb  1 19:40:21 UTC 2023
        size 128B
    }
    entries {
        name confd_image_remove
        date Wed Jul 12 19:59:42 UTC 2023
        size 142KB
    }
    entries {
        name config-object-manager
        date Wed Jul 12 20:21:50 UTC 2023
        size 55MB
    }
    entries {
        name config-object-manager-hal
        date Wed Jul 12 20:21:45 UTC 2023
        size 0B
    }
    entries {
        name events/
        date Wed Aug  2 23:18:00 UTC 2023
        size 4.0KB
    }
    entries {
        name ha
        date Tue Aug 22 21:47:05 UTC 2023
        size 29MB
    }
    entries {
        name ha-hal
        date Wed Jul 12 20:21:53 UTC 2023
        size 0B
    }
    entries {
        name ha.1
        date Tue Mar 14 13:02:02 UTC 2023
        size 101MB
    }
    entries {
        name host-config
        date Wed Aug 23 18:38:02 UTC 2023
        size 23MB
    }
    entries {
        name host-config-hal
        date Wed Jul 12 20:21:48 UTC 2023
        size 0B
    }
    entries {
        name host-config.1
        date Fri Aug 18 05:03:04 UTC 2023
        size 101MB
    }
    entries {
        name host-config.2.gz
        date Fri Jul 14 08:29:04 UTC 2023
        size 2.8MB
    }
    entries {
        name host-config.3.gz
        date Sun Jun 18 00:08:04 UTC 2023
        size 2.8MB
    }
    entries {
        name host-config.4.gz
        date Tue May 16 09:18:04 UTC 2023
        size 2.8MB
    }
    entries {
        name host-config.5.gz
        date Tue Apr 11 00:16:04 UTC 2023
        size 2.9MB
    }
    entries {
        name http_error_log
        date Wed Jul 12 20:21:54 UTC 2023
        size 9.5KB
    }
    entries {
        name httpd/
        date Sun May  7 18:26:04 UTC 2023
        size 4.0KB
    }
    entries {
        name image-server
        date Mon Aug 21 16:27:13 UTC 2023
        size 1.9MB
    }
    entries {
        name image-server-dhcp
        date Wed Aug 23 18:34:48 UTC 2023
        size 6.9MB
    }
    entries {
        name image-server-hal
        date Wed Jul 12 20:21:54 UTC 2023
        size 0B
    }
    entries {
        name image-server-httpd
        date Thu Jan 26 22:00:12 UTC 2023
        size 0B
    }
    entries {
        name image-server-monitor
        date Mon Aug 21 16:27:13 UTC 2023
        size 36KB
    }
    entries {
        name lcd.log
        date Wed Jul 12 20:07:03 UTC 2023
        size 416KB
    }
    entries {
        name logrotate.log
        date Wed Aug 23 18:38:01 UTC 2023
        size 2.2MB
    }
    entries {
        name logrotate.log.1
        date Wed Aug 23 12:17:01 UTC 2023
        size 5.1MB
    }
    entries {
        name logrotate.log.2.gz
        date Tue Aug 22 21:29:01 UTC 2023
        size 35KB
    }
    entries {
        name partition-agent
        date Wed Aug  2 23:17:43 UTC 2023
        size 2.2MB
    }
    entries {
        name partition-software-manager
        date Wed Aug 23 18:38:07 UTC 2023
        size 31MB
    }
    entries {
        name partition-software-manager.1
        date Tue Aug 22 11:46:03 UTC 2023
        size 101MB
    }
    entries {
        name partition-software-manager.2.gz
        date Fri Aug 18 07:09:04 UTC 2023
        size 3.9MB
    }
    entries {
        name partition-software-manager.3.gz
        date Mon Aug 14 02:32:03 UTC 2023
        size 3.9MB
    }
    entries {
        name partition-software-manager.4.gz
        date Wed Aug  9 21:56:03 UTC 2023
        size 3.9MB
    }
    entries {
        name partition-software-manager.5.gz
        date Sat Aug  5 17:21:04 UTC 2023
        size 3.8MB
    }
    entries {
        name partition-update
        date Wed Aug 23 18:34:45 UTC 2023
        size 35MB
    }
    entries {
        name partition-update.1
        date Sat Jul  1 17:53:02 UTC 2023
        size 101MB
    }
    entries {
        name pel_log
        date Wed Aug 23 18:34:49 UTC 2023
        size 69MB
    }
    entries {
        name reprogram_chassis_network
        date Wed Jul 12 20:22:05 UTC 2023
        size 50KB
    }
    entries {
        name rsyslogd_init.log
        date Wed Aug 23 18:34:30 UTC 2023
        size 97KB
    }
    entries {
        name run/
        date Wed Jul 12 20:21:45 UTC 2023
        size 4.0KB
    }
    entries {
        name sshd.terminal-server
        date Wed Jul 12 20:22:13 UTC 2023
        size 5.7KB
    }
    entries {
        name switchd
        date Wed Aug  2 23:17:42 UTC 2023
        size 7.5MB
    }
    entries {
        name switchd-hal
        date Wed Jul 12 20:21:46 UTC 2023
        size 0B
    }
    entries {
        name system-update
        date Wed Jul 12 20:12:46 UTC 2023
        size 26KB
    }
    entries {
        name terminal-server.default
        date Wed Aug  2 23:17:41 UTC 2023
        size 141KB
    }
    entries {
        name tftp.log
        date Wed Jul 12 20:31:43 UTC 2023
        size 977B
    }
    entries {
        name vcc-confd-go-standby-hal.102476
        date Wed Feb  1 19:40:21 UTC 2023
        size 0B
    }
    entries {
        name vcc-confd-go-standby-hal.97587
        date Mon Jan 30 18:49:00 UTC 2023
        size 0B
    }
    entries {
        name velos.log
        date Wed Aug 23 18:34:24 UTC 2023
        size 148MB
    }
    entries {
        name velos.log.1
        date Tue May  2 16:11:57 UTC 2023
        size 513MB
    }
    syscon-1-active# 


To view the contents of the **audit.log** file, use the command **file show path /log/controller/audit.log**. This will show the entire log file from the beginning but may not be the best way to troubleshoot a recent event. You can append **| more** to the command to go through the file in pages.

.. code-block:: bash

    syscon-1-active# file show log/controller/audit.log | more
    2023-07-12T15:39:16.116053-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external token authentication succeeded via rest from 172.18.105.146:0 with http, member of groups: admin session-id:admin1689190708".
    2023-07-12T15:39:16.119912-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.146:0 with http using externalvalidation authentication".
    2023-07-12T15:39:16.127755-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/16891639" cmd="created new session via rest from 172.18.105.146:0 with http".
    2023-07-12T15:39:16.130932-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/16891639" cmd="RESTCONF: request with http: GET /restconf//data/openconfig-system:system/f5-system-redundancy:redundancy HTTP/1.1".
    2023-07-12T15:39:16.218346-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external token authentication succeeded via rest from 172.18.105.146:0 with http, member of groups: admin session-id:admin1689190708".
    2023-07-12T15:39:16.221913-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.146:0 with http using externalvalidation authentication".
    2023-07-12T15:39:16.244875-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/16891641" cmd="created new session via rest from 172.18.105.146:0 with http".
    2023-07-12T15:39:16.248800-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/16891641" cmd="RESTCONF: request with http: GET /restconf// HTTP/1.1".
    2023-07-12T15:39:16.253615-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/16891641" cmd="terminated session (reason: normal)".
    2023-07-12T15:39:16.261907-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/16891641" cmd="RESTCONF: response with http: HTTP/1.1 /restconf// 200 duration 205219 us".
    2023-07-12T15:39:16.266997-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/16891639" cmd="RESTCONF: response with http: HTTP/1.1 /restconf//data/openconfig-system:system/f5-system-redundancy:redundancy 200 duration 283532 us".
    2023-07-12T15:39:16.271005-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/16891639" cmd="terminated session (reason: normal)".
    2023-07-12T15:39:17.538995-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external token authentication succeeded via rest from 172.18.105.146:0 with http, member of groups: admin session-id:admin1689190708".
    2023-07-12T15:39:17.543055-04:00 controller-1 audit-service[8]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.146:0 with http using externalvalidation authentication".
    2023-07-12T15:39:17.550559-04:00 controller-1 audit-

There are options to manipulate the output of the file. Add **| ?** to the command to see the options available to manipulate the file output.

.. code-block:: bash

    syscon-1-active# file show log/controller/audit.log | ?
    Possible completions:
    append    Append output text to a file
    begin     Begin with the line that matches
    count     Count the number of lines in the output
    exclude   Exclude lines that match
    include   Include lines that match
    linnum    Enumerate lines in the output
    more      Paginate output
    nomore    Suppress pagination
    save      Save output text to a file
    until     End with the line that matches
    syscon-1-active# file show log/controller/audit.log |

There are other file options that allow the user to tail the log file using **file tail -f** for a live tail or **file tail -n <number of lines>** to view a specific number of the most recent lines.

.. code-block:: bash

    syscon-1-active# file tail -f log/controller/audit.log
    2023-08-23T14:42:45.935724-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI aborted".
    2023-08-23T14:43:34.069068-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file show log/controller/audit.log | |'".
    2023-08-23T14:43:37.917156-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:43:53.082404-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f /log/controller/audit.log'".
    2023-08-23T14:43:53.232138-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:08.501123-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f /log/controller/audit.log'".
    2023-08-23T14:44:08.534215-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:23.639199-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file show /log/controller/audit.log'".
    2023-08-23T14:44:23.676737-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:45.535136-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f log/controller/audit.log'".

Below is an example of tailing the last 20 lines of the audit.log.

.. code-block:: bash

    syscon-1-active# file tail -n 20 log/controller/audit.log
    2023-08-23T14:34:24.310736-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'commit'".
    2023-08-23T14:34:56.335916-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'do show running-config system logging sw-components sw-component audit-service'".
    2023-08-23T14:34:56.349423-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:37:35.759037-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'exit'".
    2023-08-23T14:37:35.764733-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:38:05.691301-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file list path log/controller/'".
    2023-08-23T14:38:07.742916-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:41:01.594309-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file show log/controller/audit.log | more'".
    2023-08-23T14:42:45.935724-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI aborted".
    2023-08-23T14:43:34.069068-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file show log/controller/audit.log | |'".
    2023-08-23T14:43:37.917156-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:43:53.082404-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f /log/controller/audit.log'".
    2023-08-23T14:43:53.232138-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:08.501123-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f /log/controller/audit.log'".
    2023-08-23T14:44:08.534215-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:23.639199-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file show /log/controller/audit.log'".
    2023-08-23T14:44:23.676737-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:44:45.535136-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -f log/controller/audit.log'".
    2023-08-23T14:45:08.714615-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI done".
    2023-08-23T14:45:15.503353-04:00 controller-1 audit-service[8]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22379288" cmd="CLI 'file tail -n 20 log/controller/audit.log'".
    syscon-1-active# 

Within the bash shell if you are logged in as root, the path for the logging is different; **/var/F5/controller/log**. Note that older audit.log files are gzipped and rotated.

.. code-block:: bash

    [root@controller-1(velos-chassis1.f5demo.net) ~]# ls -al /var/F5/controller/log/
    total 1660524
    drwxr-xr-x.  5 root admin      4096 Aug 23 08:17 .
    drwxr-xr-x. 42 root root       4096 Jul 12 16:18 ..
    -rw-r--r--.  1 root root         33 Jul 12 16:22 afu-cookie
    -rw-r--r--.  1 root root    7284136 Aug 23 14:48 audit.log
    -rw-r--r--.  1 root root   10497945 Jul 12 15:39 audit.log.1
    -rw-r--r--.  1 root root     523988 May 22 17:23 audit.log.2.gz
    -rw-r--r--.  1 root root     509551 May  2 17:30 audit.log.3.gz
    -rw-r--r--.  1 root root     523266 Apr  7 14:53 audit.log.4.gz
    -rw-r--r--.  1 root root     518826 Apr  6 13:26 audit.log.5.gz
    -rw-r--r--.  1 root root    1205241 Aug  2 19:17 cc-confd
    -rw-r--r--.  1 root root          0 Jul 12 16:21 cc-confd-hal
    -rw-r--r--.  1 root root   76585515 Aug 23 14:50 cc-confd-health
    -rw-r--r--.  1 root root          0 Jul 12 16:21 cc-confd-health-diag-agent
    -rw-r--r--.  1 root root     232487 Jul 12 16:21 cc-confd-init
    -rw-r--r--.  1 root root     151721 Jul 12 16:22 cc-upgrade.dbg
    -rw-r--r--.  1 root root   79548584 Aug 12 22:48 chassis-manager
    -rw-r--r--.  1 root root  104871484 Feb 26 20:46 chassis-manager.1
    -rw-------.  1 root root          0 Jan 26  2023 confd
    -rw-r--r--.  1 root root        128 Feb  1  2023 confd_go_standby
    -rw-r--r--.  1 root root     144673 Jul 12 15:59 confd_image_remove
    -rw-r--r--.  1 root root   57388358 Jul 12 16:21 config-object-manager
    -rw-r--r--.  1 root root          0 Jul 12 16:21 config-object-manager-hal
    drwxr-xr-x.  3 root root       4096 Aug  2 19:18 events
    -rw-r--r--.  1 root root   29851294 Aug 22 17:47 ha
    -rw-r--r--.  1 root root  104858514 Mar 14 09:02 ha.1
    -rw-r--r--.  1 root root          0 Jul 12 16:21 ha-hal
    -rw-r--r--.  1 root root   23173914 Aug 23 14:50 host-config
    -rw-r--r--.  1 root root  104859854 Aug 18 01:03 host-config.1
    -rw-r--r--.  1 root root    2847624 Jul 14 04:29 host-config.2.gz
    -rw-r--r--.  1 root root    2900382 Jun 17 20:08 host-config.3.gz
    -rw-r--r--.  1 root root    2895659 May 16 05:18 host-config.4.gz
    -rw-r--r--.  1 root root    2968617 Apr 10 20:16 host-config.5.gz
    -rw-r--r--.  1 root root          0 Jul 12 16:21 host-config-hal
    drwxr-xr-x.  2 root root       4096 May  7 14:26 httpd
    -rw-r--r--.  1 root root       9632 Jul 12 16:21 http_error_log
    -rw-r--r--.  1 root root    1973541 Aug 21 12:27 image-server
    -rw-r--r--.  1 root root    7147228 Aug 23 14:44 image-server-dhcp
    -rw-r--r--.  1 root root          0 Jul 12 16:21 image-server-hal
    -rw-r--r--.  1 root root          0 Jan 26  2023 image-server-httpd
    -rw-r--r--.  1 root root      36310 Aug 21 12:27 image-server-monitor
    -rw-r--r--.  1 root root     425076 Jul 12 16:07 lcd.log
    -rw-r--r--.  1 root root    2325778 Aug 23 14:50 logrotate.log
    -rw-r--r--.  1 root root    5244230 Aug 23 08:17 logrotate.log.1
    -rw-r--r--.  1 root root      34979 Aug 22 17:29 logrotate.log.2.gz
    -rw-r--r--.  1 root root    2252844 Aug  2 19:17 partition-agent
    -rw-r--r--.  1 root root   32388160 Aug 23 14:50 partition-software-manager
    -rw-r--r--.  1 root root  104866813 Aug 22 07:46 partition-software-manager.1
    -rw-r--r--.  1 root root    4004398 Aug 18 03:09 partition-software-manager.2.gz
    -rw-r--r--.  1 root root    4004370 Aug 13 22:32 partition-software-manager.3.gz
    -rw-r--r--.  1 root root    4001982 Aug  9 17:56 partition-software-manager.4.gz
    -rw-r--r--.  1 root root    3952042 Aug  5 13:21 partition-software-manager.5.gz
    -rw-r--r--.  1 root root   36115660 Aug 23 14:49 partition-update
    -rw-r--r--.  1 root root  104859587 Jul  1 13:53 partition-update.1
    -rw-r--r--.  1 root root   72268655 Aug 23 14:49 pel_log
    -rw-r--r--.  1 root root      50325 Jul 12 16:22 reprogram_chassis_network
    -rw-r--r--.  1 root root      98627 Aug 23 14:34 rsyslogd_init.log
    drwxr-xr-x.  2 root root       4096 Jul 12 16:21 run
    -rw-r--r--.  1 root root       5737 Jul 12 16:22 sshd.terminal-server
    -rw-r--r--.  1 root root    7775788 Aug  2 19:17 switchd
    -rw-r--r--.  1 root root          0 Jul 12 16:21 switchd-hal
    -rw-r--r--.  1 root root      25772 Jul 12 16:12 system-update
    -rw-r--r--.  1 root root     144138 Aug  2 19:17 terminal-server.default
    -rw-r--r--.  1 root root        977 Jul 12 16:31 tftp.log
    -rw-r--r--.  1 root root          0 Feb  1  2023 vcc-confd-go-standby-hal.102476
    -rw-r--r--.  1 root root          0 Jan 30  2023 vcc-confd-go-standby-hal.97587
    -rw-r--r--.  1 root root  154851081 Aug 23 14:46 velos.log
    -rw-r--r--.  1 root root  536871152 May  2 12:11 velos.log.1
    [root@controller-1(velos-chassis1.f5demo.net) ~]# 

  
Viewing Logs from the webUI
--------------------------

In the current F5OS releases, you cannot view the F5OS audit.log file directly from the webUI, although you can download it from the webUI. To view the audit.log, you can use the CLI or API or download the files and then view. To download log files from the webUI, go to the **System Settings -> File Utilities** page. Here there are various logs directories you can download files from. You have the option to **Export** files to a remote HTTPS server or **Download** the files directly to your client machine through the browser.

.. image:: images/velos_security/image10.png
  :align: center
  :scale: 70%

If you want to download the main **audit.log**, select the directory **/log/controller**.


.. image:: images/velos_security/image11.png
  :align: center
  :scale: 70%


Viewing Audit Logs via F5OS CLI
-------------------------------

Example Audit Logging for Login, Logout, Login Failure, and Account Lockout
---------------------------------------------------------------------------


Below are examples seen on a remote syslog server for various login, logout, login failure, and account lockout events for F5OS. These examples are based on F5OS-C 1.6.0 or later.

----------------
Login Audit Logs
----------------

Below is an example of a client logging into the F5OS webUI. Note that the logs identify which user has logged in as well as what IP address they have logged in from.

.. code-block:: bash

    2023-08-23T18:15:51.435131-04:00 controller-1 external-authenticator:  pam_unix(password-auth:session): session opened for user admin by (uid=0)
    2023-08-23T18:15:51.461896-04:00 controller-1 external-authenticator:  pam_unix(password-auth:session): session closed for user admin
    2023-08-23T18:15:51.614165-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external authentication succeeded via rest from 172.18.105.188:0 with http, member of groups: admin session-id:admin1692828951".
    2023-08-23T18:15:51.616976-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.188:0 with http using external authentication".
    2023-08-23T18:15:51.622308-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22488445" cmd="created new session via rest from 172.18.105.188:0 with http".
    2023-08-23T18:15:51.625067-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22488445" cmd="RESTCONF: request with http: GET /restconf// HTTP/1.1".
    2023-08-23T18:15:51.627694-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22488445" cmd="terminated session (reason: normal)".
    2023-08-23T18:15:51.630328-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22488445" cmd="RESTCONF: response with http: HTTP/1.1 /restconf// 200 duration 284517 us".
    2023-08-23T18:15:51.814562-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external token authentication succeeded via rest from 172.18.105.188:0 with http, member of groups: admin session-id:admin1692828951".
    2023-08-23T18:15:51.820628-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.188:0 with http using externalvalidation authentication".
    2023-08-23T18:15:51.831604-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22488453" cmd="created new session via rest from 172.18.105.188:0 with http".
    2023-08-23T18:15:51.835919-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22488453" cmd="RESTCONF: request with http: GET /restconf//data/openconfig-system:system/f5-system-licensing:licensing HTTP/1.1".
    2023-08-23T18:15:51.965080-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22488453" cmd="RESTCONF: response with http: HTTP/1.1 /restconf//data/openconfig-system:system/f5-system-licensing:licensing 200 duration 261382 us".
    2023-08-23T18:15:51.992819-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22488453" cmd="terminated session (reason: normal)".
    

Below is an example of a client logging into the F5OS CLI. Note that the logs identify which user has logged in as well as what IP address they have logged in from.

.. code-block:: bash

    2023-01-06T17:06:57.717699-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/15001237" cmd="assigned to groups: admin".
    2023-01-06T17:06:57.717714-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/15001237" cmd="created new session via cli from 172.18.104.40:61769 with ssh".
    2023-01-06T17:06:57.728956-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/15001237" cmd="CLI 'show system state hostname'".
    2023-01-06T17:06:57.730238-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/15001237" cmd="CLI done".
    2023-01-06T17:06:57.732901-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/15001237" cmd="terminated session (reason: normal)".
    2023-01-06T17:06:57.775152-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/15001242" cmd="assigned to groups: admin".
    2023-01-06T17:06:57.775243-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/15001242" cmd="created new session via cli from 172.18.104.40:61769 with ssh".

Below is an example of a client authentication to the F5OS REST API. Note that the logs identify which user has accessed the API as well as what IP address they have sent the request from.

.. code-block:: bash

    2023-08-23T18:20:29.802066-04:00 controller-1 external-authenticator:  pam_unix(password-auth:session): session opened for user admin by (uid=0)
    2023-08-23T18:20:29.826675-04:00 controller-1 external-authenticator:  pam_unix(password-auth:session): session closed for user admin
    2023-08-23T18:20:29.964792-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="external authentication succeeded via rest from 172.18.105.188:0 with http, member of groups: admin session-id:admin1692829229".
    2023-08-23T18:20:29.967513-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/0" cmd="logged in via rest from 172.18.105.188:0 with http using external authentication".
    2023-08-23T18:20:29.973108-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22490815" cmd="created new session via rest from 172.18.105.188:0 with http".
    2023-08-23T18:20:29.976046-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22490815" cmd="RESTCONF: request with http: GET /restconf/data/openconfig-system:system/aaa HTTP/1.1".
    2023-08-23T18:20:30.214489-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22490815" cmd="RESTCONF: response with http: HTTP/1.1 /restconf/data/openconfig-system:system/aaa 200 duration 516052 us".
    2023-08-23T18:20:30.221196-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22490815" cmd="terminated session (reason: normal)".

----------------
Logout Audit Logs
----------------

Below is an example of a client logging out of the F5OS CLI. Note that the logs identify which user has logged out as well as what IP address they have logged out from.

.. code-block:: bash

    2023-08-23T18:23:18.543593-04:00 controller-1 audit-service[8]:  priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/22491444" cmd="CLI 'logout'".
    2023-08-23T18:23:18.747115-04:00 controller-1 audit-service[8]:  priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/22491444" cmd="terminated session (reason: normal)".
    2023-08-23T18:23:18.769546-04:00 controller-1 sshd[125181]:  pam_unix(sshd:session): session closed for user admin
    2023-08-23T18:23:28.528444-04:00 controller-1 HOST-audit/audit.log:  type=USER_LOGOUT msg=audit(1692829398.745:236): pid=125181 uid=0 auid=1000 ses=247494 subj=system_u:system_r:sshd_t:s0-s0:c0.c1023 msg='op=login id=1000 exe="/usr/sbin/sshd" hostname=? addr=? terminal=/dev/pts/0 res=success'


--------------------------
Account Lockout Audit Logs
--------------------------

To capture all events related to account lockout, you will need to configure F5OS to send both standard syslog events as well as host audit log events to a remote server. This is because some of the audit events related to account lockout are captured before the F5OS layer in the host/audit.log and by default that log is not sent remotely.

To forward the contents of the host audit logs add **config files file audit/audit.log** to the **system logging host-logs** configuration as seen below.

.. code-block:: bash

    syscon-1-active# show running-config system logging host-logs 
    system logging host-logs
    config remote-forwarding enabled
    config remote-forwarding include-standby
    config selectors selector AUTHPRIV DEBUG
    config files file audit/audit.log
    !
    syscon-1-active#

In addition, you'll want to ensure that **selectors selector AUTHPRIV INFORMATIONAL** is added to the **system logging remote-servers** configuration for your configured syslog location.

.. code-block:: bash

    syscon-1-active# show running-config system logging remote-servers 
    system logging remote-servers remote-server 10.255.85.164
    config remote-port 514
    config proto udp
    selectors selector LOCAL0 INFORMATIONAL
    selectors selector AUTHPRIV INFORMATIONAL
    !
    syscon-1-active#

Below is remote syslog example of a client logging into the F5OS CLI and entering an invalid password multiple times, resulting in an account lockout event. The configured password-policy for **max-login-failures** has been set to two, meaning once the client issues two invalid passwords the account will be temporarily locked for the **unlock-time** of sixty seconds. 


.. code-block:: bash

    syscon-1-active# show running-config system aaa password-policy 
    system aaa password-policy config min-length 6
    system aaa password-policy config required-numeric 0
    system aaa password-policy config required-uppercase 0
    system aaa password-policy config required-lowercase 0
    system aaa password-policy config required-special 0
    system aaa password-policy config max-letter-repeat 3
    system aaa password-policy config max-sequence-repeat 0
    system aaa password-policy config max-class-repeat 0
    system aaa password-policy config required-differences 8
    system aaa password-policy config reject-username false
    system aaa password-policy config apply-to-root true
    system aaa password-policy config retries 3
    system aaa password-policy config max-login-failures 2
    system aaa password-policy config unlock-time 60
    system aaa password-policy config root-lockout true
    system aaa password-policy config root-unlock-time 60
    system aaa password-policy config max-age 0
    syscon-1-active# 

In the logs below, a local user **testuser** has entered two consecutive bad passwords resulting in a temporary lock of the account.

.. code-block:: bash

    2023-02-21T12:23:10.495053-05:00 appliance-1 unix_chkpwd[45741]:  password check failed for user (testuser)
    2023-02-21T12:23:10.495481-05:00 appliance-1 sshd[45026]:  pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=172.18.105.83  user=testuser
    2023-02-21T12:23:18.298137-05:00 appliance-1 unix_chkpwd[46717]:  password check failed for user (testuser)
    2023-02-21T12:23:18.298942-05:00 appliance-1 sshd[45026]:  pam_faillock(sshd:auth): Consecutive login failures for user testuser account temporarily locked
    2023-02-21T12:23:20.223386-05:00 appliance-1 sshd[46957]:  pam_unix(sshd:session): session opened for user root by (uid=0)
    2023-02-21T12:23:20.274338-05:00 appliance-1 sshd[46957]:  pam_unix(sshd:session): session closed for user root
    2023-02-21T12:23:20.416710-05:00 appliance-1 HOST-audit/audit.log:  type=RESP_ACCT_UNLOCK_TIMED msg=audit(1677000190.495:250): pid=45026 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:sshd_t:s0-s0:c0.c1023 msg='pam_faillock uid=1003  exe="/usr/sbin/sshd" hostname=172.18.105.83 addr=172.18.105.83 terminal=ssh res=success'
    2023-02-21T12:23:20.416724-05:00 appliance-1 HOST-audit/audit.log:  type=ANOM_LOGIN_FAILURES msg=audit(1677000198.297:251): pid=45026 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:sshd_t:s0-s0:c0.c1023 msg='pam_faillock uid=1003  exe="/usr/sbin/sshd" hostname=? addr=? terminal=? res=success'
    2023-02-21T12:23:20.416727-05:00 appliance-1 HOST-audit/audit.log:  type=RESP_ACCT_LOCK msg=audit(1677000198.297:252): pid=45026 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:sshd_t:s0-s0:c0.c1023 msg='pam_faillock uid=1003  exe="/usr/sbin/sshd" hostname=? addr=? terminal=? res=success'

Example Audit Logging of Configuration Changes
----------------------------------------------

Below is an example audit log of the user **jim-test** entering config mode via the CLI and then changing the description for interface 20.0 and then committing the change.

.. code-block:: bash

    2023-01-06T17:44:16.790917-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI 'config'".
    2023-01-06T17:44:16.791664-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI done".
    2023-01-06T17:44:54.864806-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI 'interfaces interface 20.0 config description "This is a test"'".
    2023-01-06T17:44:54.864822-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI done".
    2023-01-06T17:44:59.392050-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI 'commit'".
    2023-01-06T17:44:59.412077-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000005 msg="audit modify" ctx="CLI" user="jim-test/15056017" path="/interfaces/interface{20.0}".
    2023-01-06T17:44:59.412156-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000006 msg="audit value set" ctx="CLI" user="jim-test/15056017" path="/interfaces/interface{20.0}/config/description" value="This is a test".
    2023-01-06T17:44:59.413541-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15056017" cmd="CLI done".

Example Audit Logging of webUI Changes
--------------------------------------

Below is an example audit log of the user **jim-test** using the webUI and then changing the VLAN membership for interface 20.0 and then committing the change.

.. code-block:: bash

    2023-01-06T17:50:43.011201-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="external token authentication succeeded via rest from 172.18.104.40:0 with http, member of groups: admin session-id:jim-test1673045408".
    2023-01-06T17:50:43.011215-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="logged in via rest from 172.18.104.40:0 with http using externalvalidation authentication".
    2023-01-06T17:50:43.014087-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065552" cmd="assigned to groups: admin".
    2023-01-06T17:50:43.014185-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065552" cmd="created new session via rest from 172.18.104.40:0 with http".
    2023-01-06T17:50:43.014470-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065552" cmd="RESTCONF: request with http: GET /restconf/ HTTP/1.1".
    2023-01-06T17:50:43.015325-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065552" cmd="terminated session (reason: normal)".
    2023-01-06T17:50:43.016496-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065552" cmd="RESTCONF: response with http: HTTP/1.1 /restconf/ 200 duration 42906 ms".
    2023-01-06T17:50:46.109658-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="external token authentication succeeded via rest from 172.18.104.40:0 with http, member of groups: admin session-id:jim-test1673045408".
    2023-01-06T17:50:46.110048-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="logged in via rest from 172.18.104.40:0 with http using externalvalidation authentication".
    2023-01-06T17:50:46.110850-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065637" cmd="assigned to groups: admin".
    2023-01-06T17:50:46.110956-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065637" cmd="created new session via rest from 172.18.104.40:0 with http".
    2023-01-06T17:50:46.111225-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065637" cmd="RESTCONF: request with http: PUT /restconf/data/openconfig-interfaces:interfaces/interface=19.0/openconfig-if-ethernet:ethernet/openconfig-vlan:switched-vlan HTTP/1.1".
    2023-01-06T17:50:46.146634-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000005 msg="audit modify" ctx="REST" user="jim-test/15065637" path="/interfaces/interface{19.0}".
    2023-01-06T17:50:46.146728-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000003 msg="audit create" ctx="REST" user="jim-test/15065637" path="/interfaces/interface{19.0}/ethernet/switched-vlan/config/trunk-vlans{503}".
    2023-01-06T17:50:46.147281-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065637" cmd="terminated session (reason: normal)".
    2023-01-06T17:50:46.148887-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065637" cmd="RESTCONF: response with http: HTTP/1.1 /restconf/data/openconfig-interfaces:interfaces/interface=19.0/openconfig-if-ethernet:ethernet/openconfig-vlan:switched-vlan 204 duration 69082 ms".
    2023-01-06T17:50:46.207531-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="external token authentication succeeded via rest from 172.18.104.40:0 with http, member of groups: admin session-id:jim-test1673045408".
    2023-01-06T17:50:46.207564-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/0" cmd="logged in via rest from 172.18.104.40:0 with http using externalvalidation authentication".
    2023-01-06T17:50:46.208310-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065642" cmd="assigned to groups: admin".
    2023-01-06T17:50:46.208414-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065642" cmd="created new session via rest from 172.18.104.40:0 with http".
    2023-01-06T17:50:46.208908-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065642" cmd="RESTCONF: request with http: GET /restconf/data/openconfig-interfaces:interfaces HTTP/1.1".
    2023-01-06T17:50:46.404290-05:00 appliance-1 audit-service[12]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="jim-test/15065642" cmd="RESTCONF: response with http: HTTP/1.1 /restconf/data/openconfig-interfaces:interfaces 200 duration 227159 ms".
    2023-01-06T17:50:46.404731-05:00 appliance-1 audit-service[12]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="jim-test/15065642" cmd="terminated session (reason: normal)".

Example Audit Logging of API Changes
------------------------------------

Below is an example audit log of the user **admin** using the API and then adding a new VLAN to the configuration. In F5OS release prior to F5OS-A 1.4.0 API audit logs captured configuration changes but did not log the full configuration payload. 

.. code-block:: bash


    2023-02-17T17:28:10.290541-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/3104052" cmd="created new session via rest from 172.18.104.20:0 with http".
    2023-02-17T17:28:10.290769-05:00 appliance-1 audit-service[11]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/3104052" cmd="RESTCONF: request with http: PATCH /restconf/data/openconfig-vlan:vlans HTTP/1.1".
    2023-02-17T17:28:10.308174-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000003 msg="audit create" ctx="REST" user="admin/3104052" path="/vlans/vlan{600}".
    2023-02-17T17:28:10.308217-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000006 msg="audit value set" ctx="REST" user="admin/3104052" path="/vlans/vlan{600}/vlan-id" value="600".
    2023-02-17T17:28:10.308280-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000006 msg="audit value set" ctx="REST" user="admin/3104052" path="/vlans/vlan{600}/config/vlan-id" value="600".
    2023-02-17T17:28:10.308319-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000006 msg="audit value set" ctx="REST" user="admin/3104052" path="/vlans/vlan{600}/config/name" value="TEST600-VLAN".
    2023-02-17T17:28:10.308819-05:00 appliance-1 audit-service[11]: priority="Notice" version=1.0 msgid=0x1f03000000000002 msg="audit" user="admin/3104052" cmd="terminated session (reason: normal)".
    2023-02-17T17:28:10.310149-05:00 appliance-1 audit-service[11]: priority="Info" version=1.0 msgid=0x1f03000000000001 msg="audit" user="admin/3104052" cmd="RESTCONF: response with http: HTTP/1.1 /restconf/data/openconfig-vlan:vlans 204 duration 57569 ms".


Downloading Audit Logs via CLI
------------------------------

Audit logs can be sent to a remote server as outlined above, but they can also be downloaded from the system if needed. Before transferring a file using the CLI, use the **file list** command to see the contents of the directory and ensure the file is there. There are two audit.log locations: **log/controller/audit.log** where most of the audit.log events are logged, and **log/host/audit/audit.log** where some lower-level events are logged.

The path below is the main audit.log.

.. code-block:: bash

    velos-1-gsa-1-active# file list path log/controller/audit.log
    entries {
        name audit.log
        date Tue Mar  4 00:20:32 UTC 2025
        size 2.5MB
    }
    velos-1-gsa-1-active# 

The path below is for lower-level audit log events like account lockouts.

.. code-block:: bash

    velos-1-gsa-1-active# file list path log/host/audit/audit.log 
    entries {
        name audit.log
        date Tue Mar  4 00:19:49 UTC 2025
        size 49MB
    }
    velos-1-gsa-1-active#

To export copies of these files off the system you can use the **file export** command to transfer the file to a remote HTTPS server, or to a remote server using SFTP, or SCP. Below is an example of transferring the log/system/audit.log to a remote HTTPS server:

.. code-block:: bash

    velos-1-gsa-1-active# file export local-file log/controller/audit.log remote-host 10.255.0.142 remote-file /upload/upload.php username corpuser insecure
    Value for 'password' (<string>): ********
    result File transfer is initiated.(log/controller/audit.log)
    velos-1-gsa-1-active#

To check on status of the export use the **file transfer-status** command:

.. code-block:: bash

    velos-1-gsa-1-active# file transfer-status 
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            |Time                
    1    |Export file|HTTPS   |log/controller/audit.log                                        |10.255.0.142        |/upload/upload.php                                          |         Completed|Sat Feb 25 16:46:28 2023

    velos-1-gsa-1-active# 

You may also transfer from the CLI using SCP or SFTP protocols. Below is an example using SCP:

.. code-block:: bash

    velos-1-gsa-1-active# file export local-file log/controller/audit.log remote-host 10.255.0.142 protocol scp insecure remote-file velos-audit.log username root
    Value for 'password' (<string>): *******
    result File transfer is initiated.(log/controller/audit.log)
    velos-1-gsa-1-active#

The file transfer-status command will show the upload of the SCP transfer as well as HTTPS or SFTP:

.. code-block:: bash

    velos-1-gsa-1-active# file transfer-status
    result 
    S.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            |Time                
    1    |Export file|HTTPS   |log/controller/audit.log                                        |10.255.0.142        |/upload/upload.php                                          |         Completed|Sat Feb 25 16:46:28 2023
    2    |Export file|SCP     |log/controller/audit.log                                        |10.255.0.142        |velos-audit.log                                           |         Completed|Sat Feb 25 16:50:06 2023

    rvelos-1-gsa-1-active# 


Downloading Audit Logs via API
------------------------------

To copy the audit.log files from the appliance to a remote https server use the following API call, you can change the local-file path depending on which audit.log you want to export. Below is an API POST call to export the log/system/audit.log to a remote server.

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/f5-utils-file-transfer:file/export

The JSON body of the API call should contain the following syntax.

.. code-block:: json

    {
        "f5-utils-file-transfer:insecure": "",
        "f5-utils-file-transfer:protocol": "https",
        "f5-utils-file-transfer:username": "corpuser",
        "f5-utils-file-transfer:password": "password",
        "f5-utils-file-transfer:remote-host": "10.255.0.142",
        "f5-utils-file-transfer:remote-file": "/upload/upload.php",
        "f5-utils-file-transfer:local-file": "log/controller/audit.log"
    }

You can then check on the status of the export via the following API call:

.. code-block:: bash

    POST https://{{velos_chassis1_system_controller_ip}}:8888/api/data/f5-utils-file-transfer:file/transfer-status

In the response the latest file transfer status will be displayed.

.. code-block:: json

    {
        "f5-utils-file-transfer:output": {
            "result": "\nS.No.|Operation  |Protocol|Local File Path                                             |Remote Host         |Remote File Path                                            |Status            |Time                \n1    |Export file|HTTPS   |log/controller/audit.log                                        |10.255.0.142        |/upload/upload.php                                          |         Completed|Sat Feb 25 17:06:00 2023\n2    |Export file|SCP     |log/system/audit.log                                        |10.255.0.142        |r109001-audit.log                                           |         Completed|Sat Feb 25 16:50:06 2023\n"
        }
    }



Downloading Audit Logs via webUI
-------------------------------

You can download either of the audit.log files from the **System -> File Utilities** page in the webUI. In the drop-down menu for the **Base Directory** select log/host, and then you can select the audit directory as seen below.  

.. image:: images/velos_security/imageaudit1.png
  :align: center
  :scale: 70%

Inside the audit directory you can then select the audit.log and then either **Download** to copy the file to your local machine via the browser or select **Export** to copy to a remote HTTPS server.

.. image:: images/velos_security/imageaudit2.png
  :align: center
  :scale: 70%

You can also select the **log/system** path to download the system audit.log.

.. image:: images/velos_security/imageaudit3.png
  :align: center
  :scale: 70%