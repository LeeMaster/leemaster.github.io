---
title: Authority Control - RBAC 0
date: 2018-05-26 20:03:25
categories:
- Authority Control
tags:
- RBAC
mathjax: true
---

# Thinking in a manager view

How do we manange our employee in a company ? How to control the resouce of a company which will be used by the all employee ? How to give the authority to a employee as a little manager ? How to control the organization or a company to work in a brief way?

In a company or an organization , we must have many roles such as employee chief general manager . These roles must have different title to do their works , and differnet title must have differnet salary and the rights to use the company resource .And in a company there are also some departments to let the manager flightly . and the departments and the company will be organized as a tree structure ...

<!--more-->

In the manager view , we can conclude this result , we can organize our Web or other software authority system in a tree structure , and now I will write some views of mine via reading the **Role-based Access Control** written by RAVl S. SANDHU.

## How to manage the all web service ?

In practice , we can know the system can be constructed in some ways , usually we use MVC pattern to design our software and in these days we use the FE/BE as C/S architecture to develop our system.

The most import thing in these design is that ,Web service will have the resource concept , we can manage the resource by the thing URL , URL in restful web interface design , act as an importmant role . We can see the all url as the resource common in a company like the paper computer and other things.

So we can manage the user in our system to use the resouce to be abstracted as touch the url use HTTP method and then ,we can use the semantics like ***POST*** to add a new resource and ***GET*** to get a resource and use ***PUT*** to update a resource in our system and the ***DELETE*** to delete a resource , but we also need a semantics to describe the authority which will be used to grant the authority to other users in the same system.

# RBAC models

## RBAC concept

The RBAC authority system which describe a system to manage the role in the system to access the resources in the all system . Role-based Access Control is the all name to RBAC which design a rule to control the all resources ,Who How What is the base concept in RBAC and Who how to access What ,we can infer that use the role and group we can organize the all primission to user in a easy way . 

## RBAC defines

### Define 0 level

$RBAC_0$ Define

* ***U*** , ***R*** ,***P*** and ***S*** (four set represent the users,roles,permissions,sessions)

* $PA \subseteq P \times R$ many-to-many permission to role assignment relation 

* $UA \subseteq U \times R$ many-to-many user to role assignment relation

* $user: S \to U$ a function mapping each session $s_i$ to the single $\left( s_i \right)$ (constant for the session's lifecycle)

* $roles: S \to 2^R$ a function mapping each session $s_i$ to a set of roles $roles \left( s_i \right) \in \left\{ r | \left( user \left( s_i \right) , r \right)\right\}$ (Which can change with time ) and session $s_i$ has the permissions $\cup _{rroles \left( s_i \right)} \left\{ p | \left( p , r \right) \in PA \right\}$


## RBAC components 

In the derivation above we can infer that , the role will contain the permissions and the user will be linked to role , and then we can use a string symbol to record the permission for a user and we can define the all permission on a role and then will be assigned to the users.

### RBAC Role

Role can be assigned to one or some permissions and then we can belive that a user must be as a role or some roles , so the roles we can see as the container to contain the permission in a system.

### RBAC Permission

And the permission we can belive that , a permission can be asssigned to a role , the role and permission is a releationship of N to N .

### RBAC User

The users in the RBAC system we can see it as a business entity which contain one to many roles .And the roles contain the permissions , and the user is the unit we have to do authentication in our own system.

### RBAC Session

When a user login a system , then it will get a role signature to represent it's identity , and we can use the inteceptor to intecept the request from web to identify it's identity .

### Group concept 

$$RBAC_0$$ can let us contribute a authority system , but to make the business easy , we should use the concept which we call as group . A group is a role in the system ,and a group can have children and also can inherit from a group or more , so we can easily create a graph to describe the all relations in the system.

We can let some users into a same group and they have the same permission to access the resource , so we can use some interface to create group and then assign the permission to this group and then we just need add users to this group.

# RBAC 0 level implementation 

RBAC system base on the relations theory in Mathmatic and we can use the relation based Database to crate a system data structure to contain the all data , so we can do this design below.

User Table 

```sql
create table user(
    user_id bigint primary key,
    user_role int not null,
    # other fields 
);
```

Permission Table

```sql
create table permission(
    permission_id bigint auto_increment primary key,
    permission_name varchar(30) not null,
    permission_url varchar(255) not null,
    permission_mask smallint not null default 31, # 11111
);
```

Role Table 

```sql
create table role(
    role_id bigint primary key auto_increment,
    role_name varchar(100) not null,
    role_parent bigint null
);

```

Role-Permission Table

```sql
create table role_permission(
    rp_id bigint primary key auto_increment,
    rp_role bigint not null,
    rp_permission bigint not null
);
```

Role-User Table

```sql
create table role_user(
    ru_id bigint primary key auto_increment,
    ru_user bigint not null,
    ru_role bigint not null
);
```

And we can describe the PA and UA operation to role use the sql to describe , when we need a new permission we just need create a record to the permission table ,and the role we can do the design to let a role can inherit from another or other roles and the user asssign role just add record to the role_user table and the permission assign to role just to add a record to the role_permission table . And the SQL are real easy .

In action we should load the all role to system cache and then create a graph to describe the network to describe the inherit relations .