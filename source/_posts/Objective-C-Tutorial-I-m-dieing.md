---
title: Objective C Tutorial - I'm dieing
date: 2018-05-24 23:14:57
categories:
- Tutorial
tags:
- Objective c
---

# What the fuck programming in OC

Recent day , my friend ask me to help him create an IOS app to make his electronic shop to be more effective . And then I choose the Objective-C , because I think this language will be more like C and Java or C++ , unfortunately this programming language is the worest language I work with.

Message transformming ? What the fuck in action , and today after I learn this language 5 days , I just know how to create a Object and Class and how to think IOS development in OOD and use OOP to write the project in OC .

I belive that I will not use this language anymore , but I have tracked in this enviroment ,I must create some notes and record the thinking in this language ! More fucking in OC ! 

<!--more-->

# Basic Grammer 

variables define and the base control flow are same like C and I don't want to waste time to talk about them .

## Block 

This character can let we implement the pattern called observer and then will use this grammer to create some anonymous function to use .

```objc

typedef void (^CompletionBlock)();
@interface SampleClass:NSObject
- (void)performActionWithCompletion:(CompletionBlock)completionBlock;
@end

@implementation SampleClass

- (void)performActionWithCompletion:(CompletionBlock)completionBlock{

    NSLog(@"Action Performed");
    completionBlock();
}

@end

int main()
{
    /* my first program in Objective-C */
    SampleClass *sampleClass = [[SampleClass alloc]init];
    [sampleClass performActionWithCompletion:^{
        NSLog(@"Completion is called to intimate action is performed.");
    }];
    
    return 0;
}
```

Now I finally the common function define in OC , block in OC just delivery a function pointer waiting the next trick in the function who call him, and the real function is the message delivering .

## Methods or Function

Decalre and define a function in my mind are not easy to get in OC , the more details will write in **Message delivery** chacpter.

We can define a function in this way 

```objective-c
- (void) add :(int) a : (int) b
{
    NSLog(@"%d",a+b)
}
```

I don't know why do this design in this language , but I gradually understand the message model to describe the actual world , in the deep learning. and the next chapter I will do more description for this character.

## Define class 

We can define a class as below .

Declare

```objc
@interface Person : NSObject
{
    @public NSString * name;
    @public NSString * address;
}

- (id) initWithName : (NSString *) name andAddress : (NSString *) address;
- (void) sayHello ;
@end
```

Implementation

```objc
@implementation Person
- (id) initWithName : (NSString *) name andAddress : (NSString *) address
{
    self = [super init]
    if(self != nil){
        self.name = name;
        self.address = address;
    }
    return self; 
}
- (void) sayHello 
{
    NSLog(@"Hello my name is %@ I am live in %@",self.name,self.address);
}
@end
```

And Call in Main 

```objc

int main()
{
    @autoreleasepool{
        Person * person = [
            [Person alloc] initWithName : @"LeeMaster" : andAddress : @"SJZ"
        ];

        [person sayHello];
    }

}

```

We can define a class use the @interface keyword and implement the class and write the definetion use @implementation , and use + to define a static method and use - to define a dynamic method.

Create an instance use the init* method , and if we have the no paramters constructor we can just use thei init which is defined by the OC compiler .

The most funny thing is the message deliver grammer which make me headache these days !

## Inheriting

The NSObject is the all class in OC world ancestor , so the class inherit from this class as Java class will inherit from the Object.

Create a parent class

```objc
@interface Humen : NSObject
{
    @protected NSString * name;
    @protected int age;
}

- (id) initWithName : (NSString * ) name : andAge : (int) age;
- (void) sayHello;
@end

@implementation Humen
- (id) initWithName : (NSString * ) name : andAge : (int) age
{
    self.name = name;
    self.age = age;
    return self;
}
- (void) sayHello
{
    NSLog(@"Hello My name is %@ and I'm %d ",self.name,self.age);
}
@end
```

Create a child class

```objc
@interface Student : Humen
{
    @public NSString * address;
}
- (void) reportAddress ; 
@end

@implementation Student
- (id) initWithName : (NSString * ) name : andAge : (int) age : 
       andAddress : (NSString *) address
{
    self.name = name;
    self.age = age;
    self.address = address;
    return self;
}
- (void) reportAddress
{
    [self sayHello];
    NSLog(@"My location is %@",self.address);
}
@end
```

```objc
int main()
{
    @autoreleasepool{
        Student * student = [[Student alloc] initWithName : @"LeeMaster" andAge : 23 : adnAddress : "SJZ"];

        [student sayHello];
        [student reportAddress];
    }
}
```

When I wrote this code as above , I suddenly understand the message delivery grammer -- the method call , and the dunamic binding in runtime , so I can delivery a message to nil but the all program don't exit with error code .

## Categories

Like Golang the Objective-C also support the duck type , it belives that when a class implement a method define in the other class , this class can be saw as that class , it's easy understand the duck can speak gaga , when a person speak gaga we can see this person as a duck !

So to benifit this character we can dynamical add property and methods to a class , we defined before , so use this character we can implement a design pattern , Adaptor-Pattern ,and Bridge-Pattern also Proxy-Pattern.

Now we have defined a class , which have contain the methods sayHello and reportAddress.

```objc
@interface Person : NSObject
{
    @public NSString * name;
    @public NSString * address;
}

- (id) initWithName : (NSString * ) name : andAge : (NSString * ) address;
- (void) sayHello;
- (void) reportAddress;
@end

@implementation Person
- (id) initWithName : (NSString * ) name : andAge : (NSString *) address
{
    self.name = naem;
    self.address = address;
    return self;
}
- (void) sayHello
{
    NSLog(@"My Name is %@",self.name);
}
- (void) reportAddress
{
    NSLog(@"My address is %@",self.adress)
}
@end
```

But now we need this class can add a property and a method we can do as this way.

```objc
@interface Person(student)
- (void) reportTime;
@end

@implementation Person(student)
- (void) reportTime
{
    NSLog(@"now time is 2018");
}
@end
```

use this cahracter we can move the method in class which contains long code lines to a new file.m and then split a class implmentation in many way and expend the origin class everytime.

## Extention 

The catergories let a class implementation splited into many files and to manage easily , the Extention really expand the class everytime and everywhere , and most time this character is used to make some private methods.

```objc
@interface Student : NSObject
- (void) learning : (int) time;
@end

@interface Student()
- (void) hardWorking;
- (void) lazyWorking;
@end

@implementation Student
- (void) learning : (int) time
{
    NSLog(@"I'm learning");
    if(time >= 10){
        [self hardWorking];
    }else{
        [self lazeWorking];
    }
}

- (void) hardWorking
{
    NSLog(@"I'm learning hard");
}
- (void) lazyWorking
{
    NSLog(@"I'm learning lazily");
}
@end
```

## Interface -> Protocal

As the interface in Java and the abstact in C++ , we can belive the protocal is the interface in Objective-C and the protocal declare the methods and all implementation of this protocal must implement the methods . But in objective-c we don't need implement all methods!

Delegate , I think this pattern is the best thing in OC , like the dynamic proxy in Java , we need a protocal and then define the must implement methods and then create a origin class and next create the proxy class to do some addtional thing in next thing.


```objc
@protocol PrintProtocolDelegate

- (void)processCompleted;

@end

@interface PrintClass :NSObject
{
    id delegate;
}

- (void) printDetails;
- (void) setDelegate:(id)newDelegate;
@end
```

```objc
@implementation PrintClass

- (void)printDetails{
    NSLog(@"Printing Details");
    [delegate processCompleted];
}

- (void) setDelegate:(id)newDelegate{
    delegate = newDelegate;
}

@end
```

```objc
@interface SampleClass:NSObject<PrintProtocolDelegate>

- (void)startAction;

@end

@implementation SampleClass

- (void)startAction{
    PrintClass *printClass = [[PrintClass alloc]init];
    [printClass setDelegate:self];
    [printClass printDetails];
}

-(void)processCompleted{
    NSLog(@"Printing Process Completed");
}

@end

```

```objc
int main(int argc, const char * argv[])
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    SampleClass *sampleClass = [[SampleClass alloc]init];
    [sampleClass startAction];
    [pool drain];
    return 0;
}
```

The origin class can do some thing base , and then call delegate to do next thing , then we can use this pattern to create some program to do in Ios develop.


## @property @synthesize

### @property attribute

### Usage Setter and Getter

Like java  setter/getter rule ,the oc have the same rule to use ,and we can do the same thing 

# Message delivery

Now this character is the more puzzle thing in the all programming in OC , I dont't know anything in the OOP in the real world , but in a deep thinking then I suddenly understand the method calling function .

For example , we have a mathmatic teacher and a english teacher , we aussume that the mathmatic teacher don't know how to teach english and the english teacher don't know how to teach the math , so we can write this code .

```objc
@interface MathTeacher : NSObject
- (void) teachMath;
@end

@implementation MathTeacher
- (void) teachMath
{
    NSLog("TEACH MATH");
}
@end

```

```objc
@interface EnglishTeacher : NSObject
- (void) teachEnglish;
@end

@implementation EnglishTeacher
- (void) teachEnglish
{
    NSLog("TEACH ENGLISH");
}
@end
``` 

If we do the same thing call EnglishTeach Object to use the teachMath method ,we will get a compile error just now . But in OC we can do the thing to call EnglishTeacher teachMath method but get no error in compiling . Because the dynamic binding in runtime .

But what is the message delivery ? I think the Object in computer just a memory segment in action and then compile time we can bind the address of a method to an Object memeory model, But the real world this thing will don't present anymore , we just know a person is a teacher , but we don't need know if he can teach the math , all thing we be confirm in the time we call him to tell us how learn math .

And the program stack , we can assume that the label paramter will be confirm and then pop the stack and to charge if the next paramter can in the identify type , and this character we call as the duck type .

Message is the parameter and a function stack ,we can transmit these thing to a stack and we can decide this paramter if we can receive , to this point we can find that is more like a closure in some dynamice programming .

## Dynamic Binding 

Dynamic binding , in Java ,can support the runtime selection for what to do next time , and select which function and method to call .

In objective-c we don't need a interface or super class to define the method to use , we belive the message is the final design in Oc , a paramter deliver to an object then the object will use the label to choose how and what to response this message , if we don't define the handler to handle message then OC will do nothing.

# Foundation library