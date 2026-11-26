



## TLDR :

- **permission** : is the individual rule stating what can or cannot be done
- **policy** : is the JSON document that holds and delivers those permissions
- **group** : a collection of IAM users that grant users the permissions of the policies which attached to it
- **Role** : works in a similar way as the group but for aws services, it's a collection of certain aws services instances that grant the latter the permissions of policies which attached to it


```
                    +------------------+
                    |    PERMISSION    |
                    | (Allow / Deny)   |
                    +--------+---------+
                             |
                       defined inside
                             |
                             v
                    +------------------+
                    |      POLICY      |
                    | (JSON Document)  |
                    +--------+---------+
                             |
                  +----------+----------+
                  |                     |
             attached to           attached to
                  |                     |
                  v                     v
         +-----------------+   +-----------------+
         |    IAM GROUP    |   |    IAM ROLE     |
         +--------+--------+   +--------+--------+
                  |                     |
            assigned to            assumed by
                  |                     |
            +-----+-----+               |
            |           |               |
            v           v               v
       +--------+  +--------+  +-----------------+
       |  USER  |  |  USER  |  |   AWS SERVICE   |
       |   O    |  |   O    |  | (EC2 / Lambda)  |
       |  /|\   |  |  /|\   |  +-----------------+
       |  / \   |  |  / \   |
       +--------+  +--------+
```


## IAM: Users & Groups

- root account should not be used or shared
- Groups only contain users, not other groups
- user can belong to no group but that is not best practice
- user can be in multiple groups
- **Least privilege principle** : don't give more permissions than a user needs


### IAM Roles

- A role is a way to give AWS services permissions to perform certain actions on aws
- Common services using roles : EC2,Lambda
- It is basically the same thing as group but for aws services 

### How to increase IMA Users security

- Password Policy : make passwords stronger by setting different password policies such as min length, require specific specialized letters, require users to change their password after a period of time, etc
- MFA: it could be Virtual MFA device such as Google Authenticator and Authy on a phone, or U2F (Universal 2nd Factor) Security Key which is a physical device, basically a flash-disc  
## IMA Policies

### IAM Policy Structure
![[Pasted image 20261122144605.png]]


## IMA Security Tools

- Both of the methods below are accessible on the account-level, the parentheses refers to on what level those tools operate at 
- **IAM Credentials report** ( on the account-level) : is a report that list all the your account's users and status of their various credentials (password last change, password last changed, access key last used, etc)
- **IAM Last Access** (on the user-level) : show the service permissions granted to a user and when those service were last accessed. 
- **Note :** Audit permissions of your account using IAM Credentials and IAM Last Access

