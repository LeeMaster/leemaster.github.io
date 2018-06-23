---
title: Java8-stream-record
date: 2018-06-20 23:13:43
categories:
- Practice
tags:
- Java
- Concurrency
---

# Java Stream Api 

Java8 support the lambda and the stream , and what is the stream conputing method in Java ?
We can do this thinking in the java stream api , or , do the same in this way why use the stream to iterate the elements in the container in java not use the tranditional collection Iteratable or Interator interfaces ?

<!--more-->

The many time we complete the workflow in a pipeline and then we will get a final result like map/reduce , so in the new api in java we can also do thing . And the interation in java why we use the stream not the original collections api to operate the elements , because of the less using memeory.

This Api based on the Jav7 Fork/Join framework , the design for the java8 stream is this , we see the collections as a data source and then we assign some middle oeprations in the way to the final result , like DFA the original collection be the initlize state and there is also a terminal state for the end .

So we can use the api to do many thing in java8 to handle the collection and then will releax our development in daily life .

# Stream concept 

I think the first time I use the stream is use the java I/O api to read/write file or socket and I also belive the stream is a pattern in the computer . What is stream ? stream is a data delivery pattern let the data from one location to other location , and the data will be spilit to the smallest unit to transport . And then the reciver also receive the data use the same method unit by unit in string the unit is byte which allocate 8 bits to contain the data.

So if we use this concept in the collection operation we can belive the data can be infinity,we just use the stream api to get and handle , but in the tranditional method we should allocate a space to save the collections total , but in stream we just need use a very samll memeoy to contain the data. because the stream use the pattern lazy-compute , when we use an object the api will product an object .

And like the workers in factory the function will be orginazed to one or more pipeline to handle the data .

```java

public void java7MapOperation(){
    List<String> list = Array.asList({//add some elements})

    for(String key : list){
        key = key + "string";
    }
}

```

use this long style code to handle the data in map will be more stupid , but in java8 

```java
public void java7MapOperation(){
   List<String> list = Array.asList({//add some elements})

   list = list.stream().map(e -> e + "string").collect(Collectors.toList());
}
```

we do this will let the handler to a pipeline part and then will handle the list in a very easy way to handle .

# Usage 

* get a stream 

* write some Intermediate

    * map (mapToInt, flatMap)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered

* write some Terminal

    * forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

* write Short-circuiting

    * anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit

## Get a stream

Now we should get stream from a list collection as the datasource we should use in the next time . This operation will be a source for the next step to use .

There are many method to get a stream , I don't list them , we just know the one truth , we have a stream and then will handle them just in any liner collections 

## Write the pipeline function

some example will write down there .



## Use as lambda 

## Inner iteration