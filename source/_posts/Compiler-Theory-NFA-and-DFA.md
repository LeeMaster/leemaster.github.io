---
title: Compiler Theory -  NFA and DFA
date: 2018-05-16 00:51:38
categories:
- Compiler
tags:
- Compiler Theory 
- Algorithms

---

# Compiler Principle

In the recent days,I was learning the JVM and how to use Golang implement a JVM for simple use , but also learning the unix kernel the process will run in ELF which is a file format of the execute file.

So I buied the ***Moden Compiler implemention*** book to learn the compiler theroy in action

As we know that not only the progrmming language need a grammer but also the common language that be used in the daily life for our commuication such as English Chinese or more.

But how to describe a language in a logical method is a very problem for me to know,and the compiler need what wo implement itself and how the GNU gcc work , It's a realy intresting thing in the way learning CS in my school life.

<!--more-->

FA（Finite Automata Machine）do loop input the character in a statement and then every character will change the machine state and when arrive at a final state then the machine will accept the state and the reset the machine inner state which contain the start endpoint and the final endpoint 

## Project management

More details will show in the next chacpter , as we know the language will use the struct in C to describe the language structure .

Context-free grammer which used to describe a language to do more details in action , we can summarize the language characters in a mathmatical method.

And then the language which writern in a character stream in a text file ,such as Hello.java , hello.c and so on , and we need some rule to modify the text to a memory model in action , and then will use a tree like structure to contain them .

We call the data structure as **Abstract Grammer Tree** which will to describe a language.

So we need a method to manage the compiler project .

Use the gcc we may hear the concept that we say as a compile unit , but also in a grammer analyzing we also need some unit to contruct a language in a method .

From a **Backus Naur** we can summary some objects to commucate to do as so .

For example : 

```text
Stm -> Stm ; Stm 
Stm -> id := Exp
Stm -> print(ExpList)
Exp -> id 
Exp -> num
Exp -> Exp Binop Exp
Exp -> (Stm,Exp)
ExpList -> Exp,ExpList
ExpList -> Exp 
Binop -> +
Binop -> -
Binop -> /
Binop -> *
```

The above Backus Naur formaule describe a program as this example 
```Go
valone := (1 + 2) ; valtwo := valone + 2 ; print(valonw,valtwo)
```

But how do we use in action to describe in C language or other ?

```C

typedef char * string;
typedef struct A_stm * A_stm;
typedef struct A_exp * A_exp;
typedef enum{A_plus,A_minus,A_times,A_div} A_binop;

struct A_stm_{
    enum{A_comundStm,A_assignstm,A_printStm} kind;
    union {
        struct {A_stm stm1 , A_stm stm2} conpund;
        struct {string id , A_exp exp} assign;
        struct {struct AexpList exps} print;
    } u ;
};

A_stm A_ConpundStm(A_stm stm1 , A_stm stm2);
A_stm A_AssaignStm(string id , A_stm stm);
A_stm A_PrintStm(A_expList exps);

struct A_exp_ {
    enum {A_idExp,A_numExp,A_opExp,A_eseqExp} kind;
    union{
        string id;
        int num;
        struct {A_exp left ; A_binop oper; A_exp right}op;
        struct {A_stm stm; A_exp exp;}eseq; 
    } u;
};

A_exp A_IdExp(string id);
A_exp A_NumExp(int num);
A_exp A_OpExp(A_exp left,A_binop oper,A_exp right);
A_exp A_EseqExp(A_stm stm,A_exp exp);

struct A_expList_ {
    enum {A_pairExpList , AlastExpList} kind;
    union {
        struct {A_exp head;A_expList tail} pair;
        A_exp last;
    } u;
}

```

The above code had contained the all grammer rule , as a example a statement will in three expressions , one is a conpund statement , two is an assginment statement , three is the print statement . As the expression in below 

```text
Stm -> Stm ; Stm (conpund)
Stm -> id := Exp (assign)
Stm -> print(ExpList) (print)
```

So we can orginaze the all grammer of a language in a method described in the mathmatics methods and then will use this methods to do some analyze works .

Do in these rule :
* A language will use a structure to describe 
* A tree(AST) will use a typedef to define a new type in use 
* every struct will contain a kind field and an union u to define this statement 
* every will use the construct to construct the structure 
* every head file will use the common head sign in use 


## Language Structure 

Now what is the language structure in the compiler theory , we can belive that a statement will be defined in a struct program language , in data structure we had learned the algorithms we call the suffix and prefix tree , from a midfix to a suffix will use the stack which is more easilier for computer to do .

so we must modify the midfix expression to a suffix expression , then use a stack to orginaze the all data .

so we can do the next thind , a backus expression will do as a language structure in will be orginaze as a tree .

for example (statement):

```text

value := 1 ; value := value + 1 ; print(value)

id binop num (assign statement )

id binop exp ( id , binop , num) (assign)

print id (print statment )

tree 1 :

    assign op
       /   \
      id   num

tree 2 : 

    assign op
     /     \
    id     + 
          /  \
         id  num

tree 3 :

    print
     /
    id 

```

We can use three AST to handle and descibe the text stream and then will handle in the next process to excute the program

# Regular Expression

How to handle the text stream to a memeory model in action ?

We can use the Regular Expression and split the stream to many segment and then use these segment to construct a AST and then will use the stack to handle and execute the language 

more details will in the next chapter 

## FA Demo 

For next chapter I will use a simple example text match algorithm method to describe the FA

We need know some concepts , character table that will contain the all characters will be used in erery term contains 

And which is the soul of the **matching** I think is that we want find a state a P(use to find) T(use to be finded), which P prefix is the T suffix , when the prefix equal suffix then we belive that the P is contain in the T and the length decide more condition.

Different or same as the KMP the FA will do in the O(n) time complex to solve the problem.

Now we define the set is {a,b,c} and all the P and T will construct in these characters so let we write a program to do this thing.

```C
#include<stdio.h>
#include<string.h>

#define int bool
#define char * string
#define 0 false
#define 1 true

typedef struct _state_
{
    int size ;
    int * trans;
} * state;

state * get_state_arr(string p,char * tab);

bool do_match(string p,string t,char * tab);



```


![From Re to NFA](http://ww1.sinaimg.cn/large/0079qc5ply1fre6u3cjx1j30n20aut8o.jpg)

## NFA and DFA Start 

## RegExp To NFA

## NFA TO DFA 

## Lex Usage 
