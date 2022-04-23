Chapter 1 Article 1

A Primer on Concurrency
=================

Let's start with a problem. PlayStation 5s are a rare commodity in 2022. James and Lola 
are both eager to get one. Luckily, they stumble upon an e-commerce website that has a single glorious piece 
available. They rush towards the checkout button and conincidentally both of them order the item 
within a few seconds of the other. Surprisingly, they both checkout successfully. But we know, 
one of them will be getting a call from a very apologetic customer care person.

We can intuitively deduce that this happened because - 
1. both orders happened at almost at the same time.
2. AND the immediate second order went through before the quantity in the inventory was reduced by the first order.

Let's look at the checkout logic (pseudocode) on the server - 

```java
function boolean checkoutItem(String itemName) {
  var item = store.getItem(itemName);

  if (item.count == 0) return false;

  item.count -= 1;  
  store.setItem(item);
  
  return true;
}
```

Here, `checkoutItem()` would definitely work had there been one single request. Or, one 
request after another request had finished execution. However, as two requests are made 
near simultaneously, data anomaly occurs. Let's break it down - 

| Time 	| Request 1 (Lola)                                                       	| Request 2 (James)                                                                                                                                                    	|
|------	|------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| 1 ms 	| Lola invokes request                                                   	|                                                                                                                                                                      	|
| 2 ms 	| `checkoutItem("ps5");`                                                 	|                                                                                                                                                                      	|
| 3 ms 	| `var item = store.getItem(itemName);`<br>here, item.count is 1         	| James invokes request                                                                                                                                   	            |
| 4 ms 	| `if (item.count == 0) return false;`<br>passes because item.count is 1 	| `checkoutItem("ps5");`                                                                                                                                               	|
| 5 ms 	| `item.count -= 1;`<br>item.count is now 0                              	| `var item = store.getItem(itemName);`<br>here, item.count is still 1 since request 1 still has not reached the line yet where we set the updated value in the store. 	|
| 6 ms 	| `store.setItem(itemName, item);`<br>item.count is set to store is 0    	| `if (item.count == 0) return false;`<br>passes because item.count is 1                                                                                               	|
| 7 ms 	| `return true`;                                                         	| `item.count -= 1;`<br>item.count is now 0                                                                                                                            	|
| 8 ms 	|                                                                        	| `store.setItem(itemName, item);`<br>item.count is set to store is 0 (again)                                                                                          	|
| 9 ms 	|                                                                        	| `return true`;                                                                                                                                                       	|

In this example, even though the algorithm works great when called only once or sequencially,
it falls apart when we try to run it in parallel (more accurate term is **concurrently**).

This shows, it's crucial to be mindful about **concurrency** in computer science.

> Definition: Concurrency is when two or more tasks can start, run, and complete in overlapping time periods. <sup>[1]</sup>

> Simplified: The situation where your code or your application performs two or more actions at the same time is called **concurrency**. The key here is, the timelines have to overlap, even if for a little while.



## When concurrency causes calamities

More often than not, whenever 2 or more concurrent processes **share the same resource** at **overlapping times**, clashes occur.

We'll discuss later in-depth

## Solving concurrency related issues

1. Locking and named locks
2. Transactions
3. Queueing
4. Atomic operations

We'll dicuss later in-depth

## Divide and conquer

We'll tackle concurrency and atomicity in many real-world and some theoratical
scenarios. Breaking these in a few broad categories - 

1. Concurrency in Single-threaded language with Javascript
2. Concurrency in Multi-threaded language with Java
3. Concurrency and atomicity in databases (SQL & NoSQL)
4. System-level concurrency, locks and queues























## Bibliography and Citations

[1] https://stackoverflow.com/a/1050257



