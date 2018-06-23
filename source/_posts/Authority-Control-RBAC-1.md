---
title: Authority Control RBAC 1
date: 2018-05-26 22:46:58
categories:
- Authority Control
tags:
- RBAC
mathjax: true
---

# The next model to optimize $RBAC_0$

We call the next model $RBAC_1$,in this model we can let the roles in our system to inherit from the upward hierarchy , and use this model we can let a system's role to be orginazed in a hierarchy method and then we can design the base role with the base permission in the lowest hierarchy and the super manager in the high hierarchy.

And a role can have more than one permission and then we can use the tree model to describe the model in the all system starting and then , we can also use the same method to do so ,with redis or other cache tools. In the algorithms we use the base DFS to find a role which will be assigned a identifier in the system application.

<!--more--> 

# Components 

## Mathmatic model

* $U S P S PA UA$ and $user$ are unchange from the $RBAC_0$

* $RH \in R \times R$ is a partial order in $R$ called the hierarchy or role dominance relation also writern in >= in nifix notion

* 



![RBAC1 DEMO](http://ww1.sinaimg.cn/mw690/0079qc5ply1frp4k2c3bpj31ao0zmaep.jpg)

We can use the Graph to construct the model in action , when the system starting , the all informations can be readed from the database and then trismit to the cache system , we can in memeory to contribute the all model and all their releartionships about the line in a graph .

