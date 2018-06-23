---
title: Algorithms Design Recursion and Divide conquer
date: 2018-05-15 09:04:37
categories:
- Algorithms
tags:
- Divide and Conquer
- Recursion
---

# Description of Recursion and Divide 

Maybe in every CS student will learn the problem of Hanoi tower as the first step to learn the **Recursion**  and **Divide**

But this problem is seem difficult and abstract in our brain . So in a easy I will summarize the common characters in our actural life

<!--more-->

## Recursion

As we know that the computer will work with the register and will use these to operate the physical memory .

The base is that the register like PC or DS:IP will record the program address when the program running .

The Recursion is like that after end of a program and then call the same address next CPU trick .

And in the view of Math , I saw that when the recursion do so , then
the problem will reduce to a less scale of problem for now . 

This surface like the **recursion formula** in learning of senquence of number in high school.

I can belive that the next state will be contained in the before state, also the next state will be described by the before state.

For example , When we got a senquence and want to know the full permutation for this senquence , what will we do in the next time to solve this problem.

This instance {a,b,c,d,e} then we will write a algorithm to get this senquence full permutation. The code will be writen in C

```c
void perm(int arr[] , int s , int e)
{
  if(s == m){

    for(int i = 0 ;i < e ;i++) cout << arr[i]; 

    cout << endl;
  
  } else {
    
    for(int i = s ; i < m ;i++){
      
      swap(arr[s] , arr[i]);

      perm(s + 1,e);

      swap(arr[s],arr[i]);
    }
  }
}
```

Let's we do some analyzes for this function to get the full permumation.

This algorithm do like we think in common , from the start of this senquence to the end every time we fix a position and let the same handler to do next time .

To be more clearly we see this senquence {1,2,3},we can easy to write the the full permumation 

* 1 2 3
* 1 3 2
* 2 1 3 
* 2 3 1
* 3 1 2
* 3 2 1 

So is easy to get this algorithm soule that we fix the first number and then move the origin first number to the next position . and then do the same operation continue . Yeah , call the same function to handle the next situation next .

This algorithm is real easy in action and the time complexity is O(n^2)

## Divide conquer

This title may do lots thing in the big data domain to handle some problem like Map/Reduce .

We can belive the Map operation is to divide and the Reduce is to conquer .

The classical problem that we call TopK.

```java
import java.io.File;
import java.util.List;
import java.util.Scanner;
import java.util.concurrent.Callable;
import java.util.concurrent.CyclicBarrier;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

/**
  * @author leemaster
  * @version V1.0
  * @Project hotel
  * @Title TopKProblem.java created on 18-3-27
  * @Package cn.leemaster.gist
  * @Describtion
  * @since v1.0
  */

public class TopKProblem{

    private static ForkJoinPool executorService = new ForkJoinPool();


    /**
     * we assume that we need the top 100 maxinum numbers form
     * more than 10billion numbers which will be divided into 
     * some files so we need a support datastructure to do something
     */
    private class TopKHeap{

        private int[] arrayHeap = new int[101];

        private int nowSize = 0;

        private void swap(int i,int j){

            int tmp = arrayHeap[i];

            arrayHeap[i] = arrayHeap[j];

            arrayHeap[j] = tmp;
            
        }

        public void adjustHeap(int adjust,int start,int end){

            int left = adjust * 2;

            int right = adjust * 2 + 1;

            int largest ;

            if(left <= end && arrayHeap[adjust] < arrayHeap[left])
                largest = left;
            
            else
                largest = adjust;

            if(right <= end && arrayHeap[largest] < arrayHeap[right])
                largest = right;
            
            if(largest != adjust){
                swap(adjust, largest);

                adjustHeap(adjust, largest, end);
            }

        }

        public void adjustBottom(int adjust){

            if(adjust >= 50) return;
            
            int parent = adjust / 2;

            int next = adjust;

            if(parent >= 1 && arrayHeap[adjust] > arrayHeap[parent])
                next = parent;
            
            if(next != adjust){
                swap(adjust, next);
                adjustBottom(next);
            }

        }

        public void insert(int in){

            if(nowSize == 100){
                int leftMin = arrayHeap[50];

                int rightMin = arrayHeap[100];

                if(in > arrayHeap[leftMin] && arrayHeap[leftMin] < arrayHeap[rightMin]){
                    arrayHeap[50] = in;

                    adjustBottom(25);

                    return;
                }

                if(in > arrayHeap[rightMin] && arrayHeap[rightMin] <= arrayHeap[leftMin]){
                    arrayHeap[100] = in;

                    adjustBottom(50);

                    return;
                }

            }else{

                arrayHeap[++nowSize] = in;

                adjustBottom(nowSize / 2);

                return;

            }

        }

        public int getMax(){

            if(nowSize == 0) return null;

            int tmp = arrayHeap[1];

            swap(1, nowSize);

            nowSize --;

            adjustHeap(1, 1, nowSize);

            return tmp;
        }

        public int getNowSize(){
            return this.nowSize;
        }

    }

    private class StasticTask implements RecursiveTask<TopKHeap>{

        private String[] files;

        int start ;

        int end ;

        public StasticTask(String[] files){
            this.files = files;
            this.start = 0;
            this.end = files.length - 1;
        }

        private StasticTask(String[] files,int start,int end){
            this.files = files;
            this.start = start;
            this.end = end;
        }

        @Override
        protected TopKHeap compute(){
            
            final TopKHeap finaHeap = new TopKHeap();

            if(start == end){

                int[] allNum = operateFile(files[start]);

                for(int i = 0; i < allNum.length ;i++){
                    finaHeap.insert(allNum[i]);
                }

                return finaHeap;
            }else{
                int mid = (start + end) / 2;

                StasticTask leftTask = new StasticTask(files, start, mid);

                StasticTask righTask = new StasticTask(files,mid+1,end);

                leftTask.fork();

                righTask.fork();

                combinHeap(leftTask.join(), righTask.join());

                return finaHeap;

            }

            return finaHeap;
        }

        private void combinHeap(TopKHeap a,TopKHeap b){

            for(int i = 0;i < a.getNowSize();i++){
                finaHeap.insert(a.getMax());
            }

            for(int i = 0;i < b.getNowSize();i++){
                finaHeap.insert(b.getMax());
            }
        }

    }
    

    private static int[] operateFile(String name){

        File file = new File(name);

        Scanner scanner = new Scanner(file);

        List<Integer> list = new ArrayList<>();

        while(scanner.hasNext()){
            list.add(scanner.nextInt());
        }

        return list.toArray();
    }

    /**
     * we assume that the numbers will be split into 1000 files
     * and will we will open the about 4 threads to work for this task
     * and then output the top 100 numbers in these files 
     */
    public static void main(String[] args){

        TopKHeap heap = new TopKHeap();

        String[] files = new String[1000];

        Future<TopKHeap> finalHeap = 
         executorService.submit(new StasticTask(files));

        executorService.shutdown();

    }

}

```

This algorithm will use the divide and conquer thinking to do the effective work in the handle operation .

Fork/Join framework will be more useful in action .

So what is the **Divide and Conquer** ?

We can draw a conclusion that when we handle the big data and then we want to handle every case , It's look like the learning process 
a person can't learn the four years course in one day . So we will learn the parts of the course in every year like Compiler , Algorithms , OS ,SQL and so on.

Divide act the role that reduce the problem scale in some effective way.

Like the Binary search , we saw that the problem scale will reduce as a 1/2 times in every recursion , so the TopK problem is like this .

