---
layout: post
title:  "Thinking through functions for the Imperative Developer"
date:  March 28th, 2016
blog: true
---

Transitioning into a functional style approach for solving problems is challenging for an imperative programmer, like myself, who learned to program using patterns with Objects.  I was so accustomed to solving my problems the Object-Oriented way that the first time I saw a snippet of Clojure code, my mind couldn't comprehend it.  Seeing an expression like `(+ 3 2)` felt so backwards.  It was puzzling to me that functional languages never use seemingly traditional variable assignments.  And although these concepts seem to fit well with my computer science background, I found comfort in sticking with my first programming language - Java.

With an imperative style approach to programming, you typically have methods which your classes implement that mutate data fields by calling methods in a sequence. This creates temporal coupling where one method call depends on the state change of a previous method call. Accordingly, to me it seemed like the most logical solution to construct an application using imperative language because everything in the real world could be represented as an object that changes through time.  However, it is important to look at different approaches and train your brain to come up with new ways of thinking and solving problems, which can often be a scary transition for an imperative developer.  Or at least, it was for me.  So, let’s take a look at some tools that are often available in functional languages.  

## Putting On a New Toolbelt

Functional languages, like Haskell and Clojure, are designed to be stateless and immutable.  The functions you write in a language like Clojure will generate new data sets by only knowing about the parameters that you pass them.  The benefit to having functions that create new data sets is that you are able to get rid of any possible side effects your code may have since they will not know about any already existing state living within your application.  In other words, every time a function is called, it will work as if it was the only time it was ever called and will return the result of merely applying the function.  It might make sense to think of the functional paradigm as just a series of mathematical transformations of data running through your application.  But, if you're not mathematician, don't fret, it’s really not as bad as it looks.

### Reduce

If I asked an imperative programmer, "how would you get the sum of numbers in a list?", they might come up with something like the following example:

    class Example {

         public int squares(int[] nums) {
            List listOfSquares = new ArrayList<Integer>();

            for(int i = 0; i < nums.length; i++)
                listOfSquares.add(nums[i]);

            return listOfSquares;
        }
    }
   
While there’s not necessarily anything wrong with this, it's important to realize that when you create objects in Java, their references are stored in memory on the Heap for it to eventually be garbage collected.  Further, a mutable data structure could potentially pose problems in a concurrent environment with many users accessing this shared state on the Heap.  This is why the characteristics of immutability with the functional paradigm tend to favor concurrent applications.  If there is no shared state, you will not have to worry about thread synchronization and race conditions.  The following code serves the same purpose as the above but is written in a functional way using [Clojure](https://clojure.org/).

    (ns example)

    (defn add-numbers [coll]
      (reduce + coll))

    ;; (add-numbers [1 2 3 4 5])
    ;; => 15

As you will see, the `reduce` function is a common tool in functional languages.  The `reduce` function takes on both a function and a collection as an argument.  In this example, the function is the `+` operator and a collection of numbers.  Reduce will loop through all the elements in the `coll` and apply the `+` function to each element.  Sticking with the example above, let’s see how we would re-write the reduce function by hand.

    (ns example)

    (defn add-numbers [f coll]
      (loop [sum 0
             nums coll]
       (if (empty? nums)
         sum
         (recur (f sum (first nums)) (next nums)))))

    ;; (add-numbers + [1 2 3 4 5])
    ;; => 15

In this last example, we go over all the elements passed in as a collection. If the loop doesn't have any items each time this loop runs, it returns the sum. Otherwise, it recursively re-calls the loop bindings `sum` and `nums`. The sum is passed back to the loop as a result of the first number in the collection, while the `nums` is reiterated by dropping the first item in the collection.
 
As of note, there's no need to explicitly tell your program to loop through a String array as you would with Java using `for` loops. The idea here is for your functions to simply take data and continually reduce your collection to get down to eventual base case that will return the value of sum.

### Map
The map function is also pretty neat. Like `reduce`, `map` will also take a function and a collection. When you give a `map` a function and a collection, it will give you back a new list with the result of each item in the collection. For example, say you wanted to get a list containing all of the squares of the numbers in a collection:

    (defn squares [coll]
      (map #(* % %) coll))

    ;; (squares [1 2 3 4 5])
    ;; => (1 4 9 16 25)

Lets try to do the same thing but using Java.

    public int squares(int[] nums) {
        List listOfSquares = new ArrayList<Integer>();

        for(int i = 0; i < nums.length; i++)
            listOfSquares.add(nums[i]);

        return listOfSquares;
    }

There's a bit more involved when using an imperative approach and mutable data structures. Although these are simple examples, when come to a point in your programming career, it might become a bit cumbersome to come up with immutable solutions with imperative paradigm. Sure, you can use the Java Collections API, but when there are functional languages like Clojure that also run on the JVM and implement these structures out of the box, there isn't much of a reason to. You'll be able to focus on what the code does as opposed to telling the computer how to do it. In turn, you might end up being a bit more productive. And a productive programmer is a always a happy programmer.

## Comfort and Happiness

And then there's [Scala](http://www.scala-lang.org/). Scala is an interesting language that takes a multi-paradigm approach to solving problems with code. It's functional in the way that the data structures are immutable, but it also takes on many characteristics of an Object Oriented language like Java. As a programmer, you'll have a bit more options on your approach. For these reasons, Scala is often though of as [a better Java](http://alvinalexander.com/scala/using-scala-as-a-better-java).

Remember that being a good programmer doesn't just involve knowing the solution to a problem. It's about seeing how to solve a problem from multiple angles and choosing how to best accomplish your task. Training your brain to come up new ways of thinking and solving problems is often tough transition for an imperative developer. And naturally, as humans, it's too easy to continue solving problems in a way that we know will work. We find comfort with sticking to what we already know, but being a programmer requires you to continuously explore and learn new ways of thinking.
