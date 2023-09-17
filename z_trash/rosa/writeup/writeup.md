

# aws rosa 

*************************************************************************************
## 00 Overview

The role that is designated for use with rosa is: 
    AP_Rosa_minimumRequiredSCP_userRole


 

The your rosa cli commands use whatever are your current the credentials for your aws cli. 
in general the steps to get rosa right is: 

- 01 use aws cli to get session token for MFA session.  
- 02 use aws cli to assume role "AP_Rosa_minimumRequiredSCP_userRole"
- 03 make sure rosa is using the expected role. 
- 04 example

*************************************************************************************

##  01 use aws cli to get session token for MFA session. 


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
        
*************************************************************************************
## 02 set the environment variables with the MFA session token info. 

Now that you have MFA creds set, you can assume the role "AP_Rosa_minimumRequiredSCP_userRole"

- a. run the follwing command:

```console
aws sts assume-role --role-arn "arn:aws:iam::530235354076:role/AP_Rosa_minimumRequiredSCP_userRole" --role-session-name AWSCLI-Session
```

- b. this will also output temorary credentials for:
```console
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```
- c. set these environemt variables with these NEW temprary credentials (which this time are for the role "AP_Rosa_minimumRequiredSCP_userRole"). on MacOS, this is done with: 
```console
export AWS_ACCESS_KEY_ID="< the_value_in_the_output >"
export AWS_SECRET_ACCESS_KEY="< the_value_in_the_output >"
export AWS_SESSION_TOKEN="< the_value_in_the_output >"
```


- e. check whoami ( should be "AP_Rosa_minimumRequiredSCP_userRole" ): 
```console
aws sts get-caller-identity
```
*************************************************************************************
## 03 make sure rosa is using the expected role. 

##### run: 
```console

rosa whoami

```
if in the output the value for "AWS ARN: " is the arn for "AP_Rosa_minimumRequiredSCP_userRole", 
then that means commands run against rosa will use the role "AP_Rosa_minimumRequiredSCP_userRole".



*************************************************************************************

## 04 Walkthrough example

 

[click here](https://ibm.box.com/s/8o2qh2fekvp7cygdi40iuky1966x7h48) 

 

 
 *************************************************************************************
## 05 helpercode

this is the python helper code. 

copy the code to a new file. I will refer to the named file as "aws_cli_MFA_helper.py"
edit the text "< your_aws_id >" ( at the end of the file).


get an MFA key from your mfa device. 






run the following: 
```console

python3 aws_cli_MFA_helper.py < key_from_MFA_device >
```


the script will output 2 groups of text, all starting with "export".


IF you want the aws cli to use your user credentials with MFA temporary credentials, 
copy the 3 exports (after "MFA Login keys: ") and paste them into the terminal window you want to use. 


else If you want to assume the "AP_Rosa_minimumRequiredSCP_userRole", 
copy the 3 userRole exports ( after "Assume Role Login keys: ")



```python 




import subprocess

import sys
import json

import os

def run_command(command, secrets=None):

    cmd = command
    # cmd = ' '.join(command)
    p = subprocess.Popen(
        cmd, shell=True,  # nosec (ignored by bandit)
        stdout=subprocess.PIPE, stderr=subprocess.PIPE,
        universal_newlines=True
    )
    out, err = p.communicate()

    if p.returncode != 0:
        secrets = secrets or []
        for s in secrets:
            cmd = cmd.replace(s, '***')
            out = out.replace(s, '***')
            err = err.replace(s, '***')
        raise RuntimeError(
            "Error while running '{}':\noutput:\n{}\n\nerror:\n{}".format(
                cmd, out, err
            )
        )
    return out





initialWarning = '''

if the values for: 
    AWS_ACCESS_KEY_ID
    AWS_SECRET_ACCESS_KEY
    AWS_SESSION_TOKEN

are set they are automatically used by the aws cli. 
if those values are old tokens, the cli will fail. 

they need to be unset. 



unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN

'''
print(initialWarning)


def mfa_login(user, token):
    # mfa_token = sys.argv[1]
    device_arn = '"arn:aws:iam::530235354076:mfa/'+ user+'"'
    mfa_token = token


    mfa_token = "aws sts get-session-token --serial-number {}  --token-code {}".format( device_arn, mfa_token )

    response = json.loads(run_command(mfa_token))
 

    print("")
    print("MFA Login keys: ")
    print()
    print('export AWS_ACCESS_KEY_ID="'+ response["Credentials"]["AccessKeyId"]+'"')
    print('export AWS_SECRET_ACCESS_KEY="'+ response["Credentials"]["SecretAccessKey"]+'"')
    print('export AWS_SESSION_TOKEN="'+ response["Credentials"]["SessionToken"]+'"')



    os.environ["AWS_ACCESS_KEY_ID"] = response["Credentials"]["AccessKeyId"]
    os.environ["AWS_SECRET_ACCESS_KEY"] = response["Credentials"]["SecretAccessKey"]
    os.environ["AWS_SESSION_TOKEN"] = response["Credentials"]["SessionToken"]





def assumeRole(role):
    assume_role_command = 'aws sts assume-role --role-arn "arn:aws:iam::530235354076:role/' +role +'" --role-session-name AWSCLI-Session'
    response = json.loads(run_command(assume_role_command))

    print("")
    print("Assume Role Login keys: ")
    print()
    print('export AWS_ACCESS_KEY_ID="'+ response["Credentials"]["AccessKeyId"]+'"')
    print('export AWS_SECRET_ACCESS_KEY="'+ response["Credentials"]["SecretAccessKey"]+'"')
    print('export AWS_SESSION_TOKEN="'+ response["Credentials"]["SessionToken"]+'"')




print(sys.argv[1])
 
mfa_login('< your_aws_id >', sys.argv[1])
assumeRole("AP_Rosa_minimumRequiredSCP_userRole")

 





```