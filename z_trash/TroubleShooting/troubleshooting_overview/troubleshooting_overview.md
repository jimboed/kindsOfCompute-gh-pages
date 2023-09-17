# Overview


## MFA

## asumming roles via console
## asumming roles via cli
## using roles to create service roles
## trust policys

## differnce between USER roles and SERVCE roles
## big "passRole" no-noS.

- DO NOT give a servicerole permission to assume ADMIN roles or USER roles.

## Rosa
- user logged in to aws, and rosa, 
        but cant run 
        ```
        rosa whoami
        ```


    - solution: 
        reset user's security credentials , logout and lock back in with rosa. 


