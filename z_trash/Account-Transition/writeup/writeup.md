

# Account permissions replicaiton pseudocode

*************************************************************************************
## 00 Overview


Objective: given that 

- Account A has users, 
- and users are in groups, 
- and groups enable access to userRoles
- and userRoles have permissions policies that can create serviceRoles and create/configre services 
- and serviceRoles are assumed by services...

and 
- we could like those users, groups, userRoles, serviceRoles, services and permission broundaries reflected in Account B...

these are the steps to execute that reflection










## US:

in Account A: 
- get lists of users, groups, and roles (called enitities)
- for each entity, get list of inline and managed policies
- for each polciy, get polciy info (policy json)

in account B: 
- create all policies
- create (if doesnt exist), users, groups, and roles
- for each user, group, and role: attach policies 



*************************************************************************************
-- in the console for Account A -- 
*************************************************************************************


    for each userRole, and serviceRole 
        use access analyzer to generate a policy (based on cloudtrail event history) that enables the minimum access
            this creates a template that needs to be filled completed

        attach generated policy to role 
            as permission boundary? 


*************************************************************************************
-- in the cli with credentials for Account A --  
*************************************************************************************
    users  = get list of users 
    roles = get list of roles
    groups = get list of groups

    policies = { this is where policy names are accumulated}

    for each user, 
        get list of inline policies attached to user
        get list of managed polices attached to user
        get list of groups user is in. 

    for each group: 
        get list of inline policies attached to group
        get list of managed polices attached to group
        

    for each role
        get list of inline policies attached to role
        get list of managed polices attached to role
            (in the case of serviceRoles, this will include permisison boundary policies)

    

    for each policy name that has been accumulated: 
        get the role info
            (which includes the default version id, and the policy, as json)


*************************************************************************************
-- in the cli with credentials for Account B -- 
*************************************************************************************
    read in data ( from previous step ) for: 
        users  
        roles 
        groups 
        policies 

    for policies: 
        create policy 
            (which includes policy json)

    
    for entity in [users, groups, roles]: 
        if entity doesnt exist: 
            create entity

        attach all policies to entity
            (including permission boundaries)

        
        if user: 
            add user to groups










## THEM:

- get list of resources (using tags) in Account A to be replicated in Account B
- for each resource in Account A, get confguration details (which includes the role that the resource is allowed to assume). 
- create resource in Account B, pass role in Account B to resource. (the role already exists in Account B.)





*************************************************************************************
-- in the cli or with credentials for Account A -- 
*************************************************************************************
    resources = (using tags and resource groups) get list of resources to replicate

    for each resource, 
        get the role that this resource is entitled to use



*************************************************************************************
-- in the cli or with credentials for Account B -- 
*************************************************************************************
    
    for each service: 
        create service
        pass appropraiate role from Account A to service in Account B











 ex: 

```console
aws resource-groups list-groups
```

```console
aws lambda get-function --function-name arn:aws:lambda:<region>:<account>:function:<functionName>
```
