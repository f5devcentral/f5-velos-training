===========================================
Automating F5OS on VELOS 
===========================================

Since F5OS is an API first architecture, everything is automatable at the F5OS layer. There are F5OS API's for every function, and the GUI and CLI are built on top of the API. API reference materials are published on clouddocs.f5.com in addtion to the most common API workflows. In addition, Terraform providers and Ansible collections are also available for F5OS, and more functionality is being added with each release of those packages.

If you want to see what API functions are available you can view the API reference documentation for the specific F5OS version you are running. As you can see, VELOS / F5OS-C have its own API reference pages and F5OS-A / rSeries have similar pages, most of the API calls are common except for those that are specific to the platform.

`F5OS-C/F5 VELOS - API <https://clouddocs.f5.com/api/velos-api/velos-api-index.html>`_

.. image:: images/automating_velos/image1.png
  :align: center
  :scale: 70%

The API workflows section has an index which maps to all the common API workflow examples in the VELOS planning guide. In addition, there is an accompanying Postman collection which can be downloaded and used within your own environment if you want to become familiar with the F5OS API.

`F5 VELOS API Workflows <https://clouddocs.f5.com/api/velos-api/velos-api-workflows.html>`_

Below is a sample of some of the workflows available in the link above, and there are many more.

.. image:: images/automating_velos/image2.png
  :align: center
  :scale: 70%

F5OS Ansible Collection
=======================

Ansible collections have been created for F5OS for some of the more common tasks. Addtional API workflows are constantly being added to the collections.


`F5OS modules Ansible collection <https://clouddocs.f5.com/products/orchestration/ansible/devel/f5os/F5OS-index.html>`_

F5OS Terraform Provider
=======================

Terraform providers have been created for F5OS for some of the more common tasks. Addtional API workflows are constantly being added to the providers. An overview of the F5OS provider is available using the link below.

`F5OS Provider Overview <https://clouddocs.f5.com/products/orchestration/terraform/latest/F5OS/f5os-index.html#f5os-index>`_

The github location of the Terraform provider files is at the following location.

`Terraform Provider F5OS v1.3.0 <https://github.com/F5Networks/terraform-provider-F5OS/releases>`_

Getting Started with F5OS Automation
====================================

If you would prefer to automate the setup of the VELOS chassis, there are F5OS-C API calls for all of the examples above. VELOS supports token-based authentication for the F5OS API's. You may send API calls to either port 8888 or port 443. The URI path will change slightly depending on which TCP port you choose to use. For API calls sent to port 443, the initial path will be **/api**, while API calls to port 8888 will start with **/restconf**. F5OS also listens on port 80 and will redirect to TCP port 443.
 

Example of API call using port 8888.  

.. code-block:: bash

    https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/aaa

Example of API call using port 443. Replace **/restconf** with **/api**.

.. code-block:: bash

    https://{{velos_chassis1_system_controller_ip}}/api/data/openconfig-system:system/aaa

 
You can send a standard API call with user/password-based authentication (basic auth), and then store the token for subsequent API calls. The X-Auth-Token has a lifetime of fifteen minutes and can be renewed a maximum of five times before you need to authenticate again using basic auth. The renewal period begins at the ten-minute point, where the API will start sending a new X-Auth-Token in the response for the next five minutes. If your API calls fail to start using the new token by the 15-minute point, API calls will start returning 401 Not Authorized. All the API examples in this guide were generated using the Postman utility. Below is an example of using password-based authentication to the VELOS F5OS management IP address. Be sure to go to the **Auth** tab and set the *Type** to **Basic Auth** and enter the username and password to log into your rSeries appliance.

.. image:: images/automating_velos/image6a.png
  :align: center
  :scale: 70%

To capture the token and save it for use in subsequent API calls, go to the **Test** option in the API call and enter the following:

.. code-block:: bash

    var headerValue = pm.response.headers.get("x-auth-token");
    pm.environment.set("x-auth-token_velos_chassis1_system_controller", headerValue);

This will capture the auth token and store it in a variable called **x-auth-token_velos_chassis1_system_controller**.

.. image:: images/automating_velos/image6b.png
  :align: center
  :scale: 70%

This will be stored as a variable in the Postman **Environment** as seen below.

.. image:: images/automating_velos/image6c.png
  :align: center
  :scale: 70%


Once the variable is stored with the auth token, it can be used instead of using basic auth on all subsequent API calls. On any subsequent API call under the **Auth** option, set the **Type** to **Bearer Token**, and set the **Token** to the variable name. Note, Postman references variables by encasing the variable name in these types of parentheses **{{Variable-Name}}**. In this case the **Token** is set to **{{x-auth-token_velos_chassis1_system_controller}}**. 

.. image:: images/automating_velos/image6d.png
  :align: center
  :scale: 70%

You must also add some required headers to any API calls sent to F5OS. It is important to include the header **Content-Type** **application/yang-data+json** and the Token header **X-Auth-Token** with a value of **{{x-auth-token_velos_chassis1_system_controller}}**. The variable and header will change depending on the destination of the API call. It can be sent to a second chassis if desired.

.. image:: images/automating_velos/image6e.png
  :align: center
  :scale: 70%


Below is an example of using the API. To set the DNS configuration (servers and search domains) for the chassis, use the following API call. For any API calls to the VELOS F5OS layer it is important to include the header **Content-Type** **application/yang-data+json** and use port 8888 as seen below:

.. code-block:: bash

  PATCH https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/dns

Below is the body of the API call which contains the desired configuration:

.. code-block:: json

  {
      "openconfig-system:dns": {
          "config": {
              "search": [
                  "olympus.f5net.com"
              ]
          },
          "servers": {
              "server": [
                  {
                      "address": "192.168.11.0",
                      "config": {
                          "address": "192.168.11.0"
                      }
                  }
              ]
          }
      }
  }

You may then view the current DNS configuration with the following API call:

.. code-block:: bash

  GET https://{{velos_chassis1_system_controller_ip}}:8888/restconf/data/openconfig-system:system/dns

Below is the output from the API query above:

.. code-block:: json

  {
      "openconfig-system:dns": {
          "config": {
              "search": [
                  "olympus.f5net.com"
              ]
          },
          "state": {
              "search": [
                  "olympus.f5net.com"
              ]
          },
          "servers": {
              "server": [
                  {
                      "address": "192.168.11.0",
                      "config": {
                          "address": "192.168.11.0",
                          "port": 53
                      },
                      "state": {
                          "port": 53
                      }
                  }
              ]
          }
      }
  }

Curl Examples
==============

Below is an API call using Curl to an rSeries system. Note, that the Curl request is using **username:password** to authenticate. In the response, an authentication token is generated by F5OS with the header **X-Auth-Token**. This token can be used for future reqests instead of using the basic authentication method until the token times out. At that point you'll need to refresh the token.

.. code-block:: bash

    prompt% curl -i -sku admin:password -H "Content-Type: application/yang-data+json"  https://10.255.2.3:443/api
    HTTP/1.1 200 OK
    Date: Sun, 10 Dec 2023 03:11:22 GMT
    Server: Apache
    Strict-Transport-Security: max-age=63072000; includeSubdomains;
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Content-Length: 90
    Content-Type: application/yang-data+json
    Pragma: no-cache
    X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzAyMTc3ODgyIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3MDIxNzgxODIsImlhdCI6MTcwMjE3Nzg4MiwicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4wLjIwMCJ9.4XGOmqj_h0b6fmyVxhpjTNdoVVMfdBlpeL9BQHp7O4c
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
    Content-Security-Policy: default-src 'self'; upgrade-insecure-requests; frame-ancestors 'none'; script-src  'self'; style-src 'self' 'unsafe-inline'; object-src 'none'; base-uri 'self'; connect-src 'self'; font-src 'self'; frame-src 'self'; img-src 'self' data:; manifest-src 'self'; media-src 'self'; worker-src 'none';

    {"ietf-restconf:restconf":{"data":{},"operations":{},"yang-library-version":"2019-01-04"}}%                                                                                                prompt% 


You may send API calls to either port 8888 or port 443. The URI path will change slightly depending on which TCP port you choose to use. For API calls sent to port 443, the initial path will be **/api**, while API calls to port 8888 will start with **/restconf**. F5OS also listens on port 80 and will redirect to TCP port 443. The API call below is sent to port 443, note the intial path will be **/api/data**. 

.. code-block:: bash

    prompt% curl -i -sku admin:password -H "Content-Type: application/yang-data+json"  https://10.255.2.3:443/api/data/openconfig-system:system/f5-system-snmp:snmp 
    HTTP/1.1 200 OK
    Date: Sun, 10 Dec 2023 03:13:23 GMT
    Server: Apache
    Strict-Transport-Security: max-age=63072000; includeSubdomains;
    Last-Modified: Fri, 01 Dec 2023 22:26:28 GMT
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Etag: "1701-469588-456629@master"
    Content-Type: application/yang-data+json
    Pragma: no-cache
    X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzAyMTc4MDAzIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3MDIxNzgzMDMsImlhdCI6MTcwMjE3ODAwMywicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4wLjIwMCJ9.m8uC4cgmRdsTQmb8SWze1hDGf3A5d5b1USjfaYLzAkc
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
    Content-Security-Policy: default-src 'self'; upgrade-insecure-requests; frame-ancestors 'none'; script-src  'self'; style-src 'self' 'unsafe-inline'; object-src 'none'; base-uri 'self'; connect-src 'self'; font-src 'self'; frame-src 'self'; img-src 'self' data:; manifest-src 'self'; media-src 'self'; worker-src 'none';
    Transfer-Encoding: chunked

    {
    "f5-system-snmp:snmp": {
        "users": {
        "user": [
            {
            "name": "snmpv3-user",
            "config": {
                "name": "snmpv3-user",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            },
            {
            "name": "snmpv3-user2",
            "config": {
                "name": "snmpv3-user2",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user2",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            },
            {
            "name": "snmpv3-user3",
            "config": {
                "name": "snmpv3-user3",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user3",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            }
        ]
        },
        "targets": {
        "target": [
            {
            "name": "snmp-trap-receiver",
            "config": {
                "name": "snmp-trap-receiver",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            },
            "state": {
                "name": "snmp-trap-receiver",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            }
            },
            {
            "name": "snmp-trap-receiver2",
            "config": {
                "name": "snmp-trap-receiver2",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.143",
                "port": 162
                }
            },
            "state": {
                "name": "snmp-trap-receiver2",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.143",
                "port": 162
                }
            }
            },
            {
            "name": "test",
            "config": {
                "name": "test",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.139",
                "port": 162
                }
            },
            "state": {
                "name": "test",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.139",
                "port": 162
                }
            }
            },
            {
            "name": "v2c-target",
            "config": {
                "name": "v2c-target",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            },
            "state": {
                "name": "v2c-target",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            }
            }
        ]
        },
        "communities": {
        "community": [
            {
            "name": "public",
            "config": {
                "name": "public",
                "security-model": ["v1", "v2c"]
            },
            "state": {
                "name": "public",
                "security-model": ["v1", "v2c"]
            }
            },
            {
            "name": "public2",
            "config": {
                "name": "public2",
                "security-model": ["v1", "v2c"]
            },
            "state": {
                "name": "public2",
                "security-model": ["v1", "v2c"]
            }
            }
        ]
        },
        "engine-id": {
        "config": {
            "value": "mac"
        },
        "state": {
            "engine-id": "80:00:2f:f4:03:00:94:a1:8e:d0:00",
            "type": "mac"
        }
        }
    }
    }
    prompt%

You may send API calls to either port 8888 or port 443. The URI path will change slightly depending on which TCP port you choose to use. For API calls sent to port 443, the initial path will be **/api**, while API calls to port 8888 will start with **/restconf**. F5OS also listens on port 80 and will redirect to TCP port 443. The API call below is sent to port 8888, note the intial path will be **/restconf/data**. 

.. code-block:: bash

    prompt% curl -i -sku admin:password -H "Content-Type: application/yang-data+json"  https://10.255.2.3:8888/restconf/data/openconfig-system:system/f5-system-snmp:snmp 
    HTTP/1.1 200 OK
    Date: Sun, 10 Dec 2023 03:15:23 GMT
    Server: Apache
    Last-Modified: Fri, 01 Dec 2023 22:26:28 GMT
    Cache-Control: private, no-cache, must-revalidate, proxy-revalidate
    Etag: "1701-469588-456629@master"
    Content-Type: application/yang-data+json
    Pragma: no-cache
    X-Auth-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJTZXNzaW9uIElEIjoiYWRtaW4xNzAyMTc4MTIzIiwiYXV0aGluZm8iOiJhZG1pbiAxMDAwIDkwMDAgXC92YXJcL0Y1XC9zeXN0ZW0iLCJidWZmZXJ0aW1lbGltaXQiOiIxMDAiLCJleHAiOjE3MDIxNzg0MjMsImlhdCI6MTcwMjE3ODEyMywicmVuZXdsaW1pdCI6IjUiLCJ1c2VyaW5mbyI6ImFkbWluIDE3Mi4xOC4wLjIwMCJ9.qMobGzZ1KHzJum73Bnd1TGpoKP9A9xZTDAGq0tWLBn0
    Content-Security-Policy: default-src 'self'; block-all-mixed-content; base-uri 'self'; frame-ancestors 'none';
    Strict-Transport-Security: max-age=15552000; includeSubDomains
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: 1; mode=block
    Transfer-Encoding: chunked

    {
    "f5-system-snmp:snmp": {
        "users": {
        "user": [
            {
            "name": "snmpv3-user",
            "config": {
                "name": "snmpv3-user",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            },
            {
            "name": "snmpv3-user2",
            "config": {
                "name": "snmpv3-user2",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user2",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            },
            {
            "name": "snmpv3-user3",
            "config": {
                "name": "snmpv3-user3",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            },
            "state": {
                "name": "snmpv3-user3",
                "authentication-protocol": "md5",
                "privacy-protocol": "aes"
            }
            }
        ]
        },
        "targets": {
        "target": [
            {
            "name": "snmp-trap-receiver",
            "config": {
                "name": "snmp-trap-receiver",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            },
            "state": {
                "name": "snmp-trap-receiver",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            }
            },
            {
            "name": "snmp-trap-receiver2",
            "config": {
                "name": "snmp-trap-receiver2",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.143",
                "port": 162
                }
            },
            "state": {
                "name": "snmp-trap-receiver2",
                "user": "snmpv3-user",
                "ipv4": {
                "address": "10.255.0.143",
                "port": 162
                }
            }
            },
            {
            "name": "test",
            "config": {
                "name": "test",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.139",
                "port": 162
                }
            },
            "state": {
                "name": "test",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.139",
                "port": 162
                }
            }
            },
            {
            "name": "v2c-target",
            "config": {
                "name": "v2c-target",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            },
            "state": {
                "name": "v2c-target",
                "community": "public",
                "security-model": "v2c",
                "ipv4": {
                "address": "10.255.0.144",
                "port": 162
                }
            }
            }
        ]
        },
        "communities": {
        "community": [
            {
            "name": "public",
            "config": {
                "name": "public",
                "security-model": ["v1", "v2c"]
            },
            "state": {
                "name": "public",
                "security-model": ["v1", "v2c"]
            }
            },
            {
            "name": "public2",
            "config": {
                "name": "public2",
                "security-model": ["v1", "v2c"]
            },
            "state": {
                "name": "public2",
                "security-model": ["v1", "v2c"]
            }
            }
        ]
        },
        "engine-id": {
        "config": {
            "value": "mac"
        },
        "state": {
            "engine-id": "80:00:2f:f4:03:00:94:a1:8e:d0:00",
            "type": "mac"
        }
        }
    }
    }
    prompt%

