

# DLC connectivity troubleshooting

*************************************************************************************
## 00 Overview


- Enable access
    - add user's public ip to bastion authorized keys: 
        - cat ~/.ssh/id_rsa.pub
        - add the output to bastion : 
            - vi ~/.ssh/authroized_keys
    - add user's ip to bastion security group

- create troubleshooting playbook
    1. check loadbalancer health
    2. log in to instance and check dlc is running:
        - sudo systemctl status dlc
        - restart with: sudo systemctl restart dlc

    3. check instacne memory
    4. check logs, remove if nessicary

    5. reboot instance
    6. rebuild instance.

- Automatically remove unneeded logs