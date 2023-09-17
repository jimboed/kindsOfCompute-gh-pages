<!-- 

# ServiceLine Addon config: 



## Overview: 

Below is an overview of the general steps to complete. 
In secitons that follow, more details steps are provided for the overview. 



To do for all the service lines to get vpce_url: 
1. find security section
2. update VPC Endpoint in syslogforwarder serion 
3. Find <vpcname>-sos-vpce
4. Copy 1st DNS Name

 
If security addon is NOT already installed
1. Make sure logforwarder addon is installed as ready above
2.  wait for cluster to come back to ready
3. Add to CR Spec with vpce from instructions above
4. wait for cluster to come back to ready



 
If security addon is installed
1. set enabled false
2. wait for cluster to come back to ready
3. change vpce
4. set enabled true
5. wait for cluster to come back to ready





## To do for all the service lines to get vpce_url: 
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
```
a. in the console, navigate to the vpc dashboard. 
b. navigate to the "Endpoints" section
c. select the endpoint with the corresponding serviceline name
    ex: 
        if cluster is apic-d-j01
        endpoint is apic-d-vpc-j01-sos-vpce
```
3. copy the FIRST available DNS name. 
4. update VPC Endpoint in syslogforwarder section  -->
