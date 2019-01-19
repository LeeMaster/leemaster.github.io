---
title: shiro-source-1
date: 2018-11-14 00:59:18
tags:
- Java
---

# Base architecture

![](http://ww1.sinaimg.cn/large/0079qc5ply1fx6xftv975j30el0c6n1b.jpg)

* Subject 

represent a entity of a request or some operations in a system.

* Subject DAO 

support the subject to create or recorver from a request 

* Security Manager
    * Authenticator
        * Authentication strategy
    * Authorizer
    * Session Manager
    * Session DAO 
        * Cache
    * Realm
* Cache manager 

# Start to read 

The RBAC model split the user from the permission using the bradge called roles , the roles will be related with the permissions and the user should be some roles.

when the Authorizating raise , the system should firstly check 'Who is the user accessing now?' and then check 'What should the user do?' and the lastly use the permissions to check if the user has the right access right .

## Create a Subject 

A user access in our system then will get or carray a subject ,and use the subject we can infer some role and the permission informations from the subject , one way we can use the subject that carried the user id to let our system to get some informations from our database.

Next the system should check the user if he can access to this resource or the method on our server.

But the badest thing is that we should write some redundancy code in our application , which could check if the user has the right access .

Furtuantly we have the spring aop ,who help us to resolve the problem of the redundancy.

