---
title: Spring in action - Bean and Context
date: 2018-06-22 22:37:30
categories:
- Practice
tags:
- Java
- Spring
---

# Context the stage of a Bean 

We can know this thing that the bean will be defined in the context obeject in spring and then will let the bean in their own lifecycle to do something in action . In my opnion that the all framework will have a context when we develeop an application there will save many information we need .

<!--more-->

## What is the context

When firstly heard this concept ,we could be puzzled in this thing, and we can ask ourselves what is the context in spring? As many people I had be puzzled in this concept and I don't know how to extend my application for example I will make an implementation of the RBAC model in the enterpresie application，maybe I will want to load some authorities in the initiation of the application , but there are another feature that I will expose the module to a jar and then will use this jar anywhere need the authority . But the spring just support the POJOs which are just the model in programming in a software to contain the data to use like search from a database .

But we could need the mybatis to do as the data access layer framework to use , then there will be trouble when I use the @Resource annotation to aware the module in another to use ,there will have an error NullPointerException in the runtime .

So for solving this problem for the enterprise integration of a module then we should expose the module to the all application to solve the problem of NullPointerException .

Okey we can see the context as waht ? I think this object in singlton pattern in Spring will like a warehouse we can get anything we may attention to , and the all runtime informations will be saved in this location .

So there is a method to construct a large or huge project in develop , we call the micro kernel driven pattern , which let we should make an implementation firstly is the context , when we have a context then we can do many thing , include get set modify and update , so in Spring framework we can modify many thing by ourselves .

And we call the point for use to inject the objects in java as the mout point . I never think the POJOs like the bean , I think the bean is the java objects which will hold some duties in the all lifecycle in Spring , and POJOs are the sample object like DTO object .

## What is the session context

As we know that the session is the signature for a user to let the server identify their identity under the HTTP/S protocal and the stateless character will let the server and the client to be puzzle .

so we have to find a method to record the user base information in the trsacation in on socket , and the all user informations will be in a object in Java or other language ,we describe the request and response as an object to be given to one user this request scope.

As we know the servlet have the HttpSession for the programmer to use to record the user state , which will be managed by the Tomcat or Jetty the servlet container .

Not oonly the Web based on Http have the session context , in the real world there are also many scenes to use the session concept , we could 

## what is the global context 

# Lifecycle in Spring to extend 

## ApplicationContext interfaces  