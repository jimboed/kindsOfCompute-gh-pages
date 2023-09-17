

# ServiceLine Addon config 2: 



## Overview: 

Below is an overview of the general steps to complete.  



ex:
    https://github.ibm.com/automation-saas/asp-clusters/blob/master/CRs/dev/ap-cp-001.yaml
    
#### Prerequisites
- Install LogForwarder addon if not installed && wait for cluster to come back into a ready state (Config Below)

```
logforwarder:
    config:
        loglevel: info
    enabled: true
    secrets: {}

```


#### If security addon is NOT already installed

- Make sure logforwarder addon is installed as mentioned above
- Be sure the cluster is ready
- Add to CR Spec with vpce from instructions below
- wait for cluster to come back to ready


1. in the serviceline CR
        locate find security section
```
security:
    config:
        syslogforwarder:
            url: "<vpce_url>:6514"  <---- this should be updated
        enabled: true
        secrets:
            dlc-cert: dlc-cert
            syslog-forwarder: syslog-forwarder
```

2. Find <vpcname>-sos-vpce

    a. in the console, navigate to the vpc dashboard. 
    b. navigate to the "Endpoints" section
    c. select the endpoint with the corresponding serviceline name
        ex: 
            if cluster is apic-d-j01
            endpoint is apic-d-vpc-j01-sos-vpce


![Screenshot](assets/vpc_eendpoint_dns_example.png)

3. copy the FIRST available DNS name. 
4. update VPC Endpoint in syslogforwarder section 

     


    
#### If security addon was previously installed

- set enabled false on the security addon
```
security:
    config:
        syslogforwarder:
            url: "<vpce_url>:6514"  
        enabled: false   <---- change this to "false"
        secrets:
            dlc-cert: dlc-cert
            syslog-forwarder: syslog-forwarder
```


- wait for cluster to come back to ready
- change vpce to the one gathered above

```
security:
    config:
        syslogforwarder:
            url: "<vpce_url>:6514"  <---- this should be updated
        enabled: false
        secrets:
            dlc-cert: dlc-cert
            syslog-forwarder: syslog-forwarder
```

- set enabled true on the security addon

```
security:
    config:
        syslogforwarder:
            url: "<vpce_url>:6514"  
        enabled: trues   <---- change this to back to "true"
        secrets:
            dlc-cert: dlc-cert
            syslog-forwarder: syslog-forwarder
```

- wait for cluster to come back to ready











 