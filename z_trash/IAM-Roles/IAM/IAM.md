

#  How use IAM in aws. 

*************************************************************************************
## 00 Overview








- 01 Users
- 02 Groups
- 03 Roles
    - a. user Roles
    - b. service Roles


- 04 Roles: assuming userRoles to create serviceRoles
- 05 Example
 
*************************************************************************************
## 01 Users

Users are added to the account with their email. 

Users need to enable multifactor authentication. 



*************************************************************************************
## 02 Groups

Instead of adding permissions to each user, we add permissions to groups, 
and then associate a user with a group. If a user is a member of a group, 
that user has the permissions of the group. 

*************************************************************************************
## 03 Roles: Assuming roles
The role user is allowed to assume is based on the group the user is a member. For example if a user is present in the "Automation_Platform_Build_Global_ServiceSRE_LS" group, the user can assume the corresponding "AP_LS_ServiceSRE_userRole" role.
 
It is reccommended by AWS use roles where ever possible, 
since role based authentication is handled by time expiring tokens. 


We are enabling most permissions through roles. 

Roles are given permssions, and users assume a role in order to take on the abilites
they need to accomplish tasks. 

*************************************************************************************

#### to assume Roles in the console: 

The groups with the corresponding roles are as below:

| Groups	                                                | Corresponding roles                   |
|-----------------------------------------------------------|---------------------------------------|
| Automation_Platform_Build_Global_ServiceSRE_APIC          | AP_APIC_ServiceSRE_userRole           |
| Automation_Platform_Build_Global_ServiceSRE_AppConnect    | AP_Appconnect_ServiceSRE_userRole     |
| Automation_Platform_Build_Global_PlatformSRE_AutoPlatform | AP_Autoplatform_PlatformSRE_userRole  |
| Automation_Platform_Build_Global_ServiceSRE_AutoPlatform  | AP_Autoplatform_ServiceSRE_userRole   | 
| Automation_Platform_Build_Global_Backup                   | AP_Backup_userRole                    |
| Automation_Platform_Build_Global_ServiceSRE_LS            | AP_LS_ServiceSRE_userRole             |


Follow these links to assume the appropiate role: 

[AP_APIC_ServiceSRE_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_APIC_ServiceSRE_userRole&account=automation-platform-build])

[AP_Appconnect_ServiceSRE_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Appconnect_ServiceSRE_userRole&account=automation-platform-build])

[AP_Autoplatform_Controlplane_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Autoplatform_Controlplane_userRole&account=automation-platform-build])

[AP_Autoplatform_PlatformSRE_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Autoplatform_PlatformSRE_userRole&account=automation-platform-build])

[AP_Autoplatform_ServiceSRE_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Autoplatform_ServiceSRE_userRole&account=automation-platform-build])

[AP_Backup_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Backup_userRole&account=automation-platform-build])

[AP_LS_ServiceSRE_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_LS_ServiceSRE_userRole&account=automation-platform-build])

[AP_Rosa_minimumRequiredSCP_userRole](https://signin.aws.amazon.com/switchrole?roleName=AP_Rosa_minimumRequiredSCP_userRole&account=automation-platform-build])




*************************************************************************************

#### to assume Roles in the aws CLI


To configre your aws cli, you use
```console
aws configure
```
However, if the following environment variables are set, aws cli will use those as credentials: 
```console
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```

If these enviroment variables are not set, aws cli with use the credentials set up with "aws configure"
BUT, to use the cli (with those credentials) you must get an MFA (multifactor authentication) session token. 

*************************************************************************************

##### If MFA is required for the user: (If MFA is not required, skip this part. )

- a. Get your user MFA device arn in the aws console. it is in the form :
```console
arn:aws:iam::<accountNumber>:mfa/<userEmail>
```
- b. Get a MFA passkey from your MFA device. 
- c. Use those values in the following: 
```console
aws sts get-session-token --serial-number <MFA_device_arn>  --token-code <MFA_passkey_from_device>
```
- d. this will output temorary credentials 
```console
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```
- e. set these environment variables with the temprary credentials. . on MacOS, this is done with: 
 
```console
export AWS_ACCESS_KEY_ID="< the_value_in_the_output >"
export AWS_SECRET_ACCESS_KEY="< the_value_in_the_output >"
export AWS_SESSION_TOKEN="< the_value_in_the_output >"
```
- f. check whoami ( should be your email ): 
```console
aws sts get-caller-identity
```
Now that you have MFA creds set, you can assume a role.
*************************************************************************************
##### Get Temporary tokens for role: 

- a. run the follwing command:

```console
aws sts assume-role --role-arn "arn:aws:iam::< account >:role/< role_to_assume >_userRole" --role-session-name < AWSCLI-Session > 
```

- b. this will also output temorary credentials for:
```console
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```

- c. set these environemt variables with these NEW temprary credentials (which this time are for the role. on MacOS, this is done with: 
```console
export AWS_ACCESS_KEY_ID="< the_value_in_the_output >"
export AWS_SECRET_ACCESS_KEY="< the_value_in_the_output >"
export AWS_SESSION_TOKEN="< the_value_in_the_output >"
```


- e. check whoami ( should be the role to assume ): 
```console
aws sts get-caller-identity
```


*************************************************************************************



## 04 Roles: assuming userRoles to create serviceRoles

```console
user -> ASSUMES -> userRole

userRole -> CREATES -> serviceRole 

userRole -> CREATES -> AWS_Service      &&     userRole -> ASSIGN -> AWS_Service( serviceRole )

```




We distinguish between 2 kinds of roles: 
userRoles and serviceRoles.

A user takes on a userRole, and creates a serviceRole for a service to assume. 

No service should be granted permission to assume a userRole. 


If a user assumes a userRole in order to create a serviceRole for a service, 
that user has restrictions on the service role they can create. 

Both of the following must be true: 
```console

1. the serviceRole must be named following the role domain naming convention.
    for example, 
    - if the user has assumed the role "AP_Backup_userRole"
        when they create a service role, it should be named: 
        "AP_Backup_serviceRole_< choose_a_name >"
            example: 
                "AP_Backup_serviceRole_rds_393838"

    - if the user has assumed the role "AP_APIC_ServiceSRE_userRole"
        when they create a service role, it should be named: 
        "AP_APIC_ServiceSRE_serviceRole< choose_a_name >"
            example: 
                "AP_APIC_ServiceSRE_serviceRole_console_reader_12"


2. the service must be created with the permission Boundary associated with the role domain. 

    for example, 
    - if the user has assumed the role "AP_Backup_userRole"
        when they create a service role, it must be created with the permission Boundary called: 
        "AP_Backup_permissionBoundary"

    - if the user has assumed the role "AP_APIC_ServiceSRE_userRole"
        when they create a service role, it must be created with the permission Boundary called: 
        "AP_APIC_ServiceSRE_permissionBoundary"


```




If these 2 requirements are not met, the serviceRole cannot be created. 


Once a serviceRole is created, it can be passed to a service by the userRole in the provisioning and configuration of resources. 


 
*************************************************************************************

## 05 Walkthrough Example
 

[click here](https://ibm.box.com/s/rs07q3tu1ym5sax8mv4dw9abkpj8vj43) 

