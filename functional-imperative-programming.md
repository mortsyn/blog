---
layout: post
title:  ""
date:  March 28th, 2016
blog: true
---

For an imperative programmer who learned to program using patterns with Objects, making the transition to solve problems in a functional way was challenging for me. I was so accustomed to solving my problems the Object-Oriented way, that the first time I saw a snippet of Clojure code, my mind couldn't comprehend it. Seeing an expression like `(+ 3 2)` just felt so backwards. The concept that functional languages never use seemingly traditional variable assignments puzzled me. Although these concepts seem to fit well with my Computer Science background, but I found comfort in sticking with my first programming language - Java.

In an imperative style approach to programming, you would typically have methods that your classes implement which mutate your data fields by calling methods in a sequence. This creates a temporal coupling where one method call depends on the state change of a previous method call. I used to think constructing your application like this might seem like the most logical solution, since everything in the real world could be represented as an object that changes through time. Training your brain to come up new ways of thinking and solving problems is often scary transition for an imperative developer. I know it was for me. So lets take a look at some good tools that are often available in functional languages.

# Putting On a New Toolbelt

Functional languages like Haskell and Clojure are designed to be stateless and immutable. Since the functions you write in a language like Clojure will generate new data sets with only the parameters that you pass into the function. By having functions that create new data sets by only knowing about it's parameters gets rid of any possible side effects your code may have since functions will not know about any already existing state living within your application. In other words, every time a function is called, it will work as if it was the only time it was ever called and return the result of applying the function. You can think of functions as just a series of mathematical transformations on data running through your application. If you're not mathematician, don't fret, its really not as bad as it looks.

## Reduce

If I asked you, "How would you get the sum of numbers in a list?". The imperative programmer might come up with something like this, a class that contains a `List` with a getter method to get the reference to that list.

    class ExampleList {
        List array = new ArrayList<Integer>();

        public void addToList(int num) {
            array.add(num)
        }

        public List getListOfNumbers() {
            return array;
        }
    }

Now theres nothing wrong with this necessarily, but it's important to realize that when you create objects in Java, their references are stored in memory on either the Heap for it to be eventually garbage collected. This mutable data structure could potentially pose problems in a concurrent environment with many users accessing this shared state on the Heap. This is why the characteristics of immutability with the functional paradigm tend to favor concurrent applications. If there is no shared state, you will not have to worry about `synchronization` and race conditions. The code below is effectively the same thing, but just written in a functional way using [Clojure](https://clojure.org/).

    (ns example)

    (defn add-numbers [coll]
        (reduce + coll))

    ;; (add-numbers [1 2 3 4 5])
    ;; => 15

The `reduce` function is a common tool in functional languages. The `reduce` function takes both a function and a collection as an argument. In this case, the function is the `+` operator and a collection of numbers. `reduce` will loop through all the elements in the `coll` and apply the + function to each element. Lets see how we would re-write the reduce function by hand.

   (ns example)

   (defn add-numbers [f coll]
     (loop [sum 0
            nums coll]
       (if (empty? nums)
         sum
         (recur (f sum (first nums)) (next nums)))))

    ;; (add-numbers + [1 2 3 4 5])
    ;; => 15

In this last example, we go over all the elements passed in as a collection. Each time this loop runs, if the loop doesn't have any items, it returns the sum. Otherwise, it recursively re-calls the loop bindings `sum` and `nums`. The sum is passed back to the loop as a result of the first number in the collection, while the `nums` is reiterated by dropping the first first item in the collection.

There's no need to tell your program to explicitly tell your program to loop through a String array as you would with Java using `for` loops. The idea here is for your functions simply take data, and continually reduce your collection to get down to eventual base case that will return the value of sum.

## Map

The map function is also pretty neat. Like `reduce`, `map`, it will also take a function and a collection. When you give a `map` a function and a collection, and it will give you back a new list with the result of each item in the collection. Lets say you wanted to get a list containing all of the squares of the numbers in a collection.

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

# Comfort and Happiness

And then there's [Scala](http://www.scala-lang.org/). Scala is an interesting language that takes a multi-paradigm approach to solving problems with code. It's functional in the way that the data structures are immutable, but it also takes on many characteristics of an Object Oriented language like Java. As a programmer, you'll have a bit more options on your approach. For these reasons, Scala is often though of as [a better Java](http://alvinalexander.com/scala/using-scala-as-a-better-java).

Being a good programmer doesn't just involve knowing the solution to a problem. It's about seeing how to solve a problem from multiple angles and choosing how to best accomplish your task. Training your brain to come up new ways of thinking and solving problems is often tough transition for an imperative developer. And naturally, as humans, it's too easy to continue solving problems in a way that we know will work. We find comfort with sticking to what we already know, but being a programmer requires you to continuously explore and learn new ways of thinking.
