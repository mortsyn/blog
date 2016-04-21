---
layout: post
title:  "Functional Programming: An Imperative Comparison"
date:  March 28th, 2016
blog: true
---

Recently, I have been introduced to the world of functional programming using [Clojure](https://clojure.org/). Prior to learning this style of programming, I spent the majority of my time as programmer writing code using an imperative language like Java. Imperative languages solve problems with a heavy reliance on classes, for loops, and mutable data structures. To contrast to the imperative paradigm, the functional paradigm incorporates an entirely different way of solving your everyday programming problems, mainly by utilizing immutable data structures and the ability to pass functions into methods as parameters. Moreover, functional programming requires a different way of thinking and it is often a challenging transition for those who are used to more mainstream languages like Java. Therefore, I would like to lay out some differences to make this transition easier.

## Welcome to Functional Programming

To understand how functional programming differs from it's imperative counterpart, there are a few concepts you need to understand. Most importantly is the fact that functional languages are designed to be stateless and immutable. If you are used to creating objects with fields and methods that operate on these variables, say goodbye, because in the functional paradigm, seemingly traditional assignment statements are generally discouraged. With the functional paradigm, the functions you write and use will only depend on its parameters to get the job done and will always return a result.

Every time a function is called, it will work as if it was the only time it was ever called during runtime and will return the result of merely applying the function.  It might make sense to think of functional programming as a series of transformations of the data running through your application.  But, if you're no mathematician, don't fret, it’s really not as bad as it might sound.   

## Putting On a New Toolbelt

With Clojure and many other functional languages, functions are high-order, which means that they can take one or more functions as an argument. Because of this, you can effectively string together many small functions in sequence, which makes your code reusable and easily testable. Lets take a look at some high-order functions that are often used in functional languages.

### Map

The `map` function is a common built-in method in many functional languages, including Clojure. The way it works is simple, it takes a function and a collection of data, and it returns a new collection with the function applied to every element in the original collection. A collection can be represented as a list, vector, hashmap, or even a set. Using an imperative approach, lets see how we can implement a function that takes a list of temperatures in fahrenheit and returns a new list of temperatures converted to celsius.

    public class TemperatureConverter {

        public List<Integer> mapToCelsius(List<Integer> temperatures) {
            List<Integer> convertedTemperatures = new ArrayList();

            for(int i = 0; i < temperatures.size(); i++)
                convertedTemperatures.add(convertToCelsius(temperatures.get(i)));

            return convertedTemperatures;
        }

        private int convertToCelsius(int temperature) {
            return (temperature - 32) * 5 / 9;
        }
    }

As you see, the function `mapToCelsius` first instantiates a new `List<Integer>`, iterates through the given temperatures and calls `convertToCelsius` on each temperature and returns the list of results. This solution is immutable, since every time `mapToCelsius` is called, it returns a new `List<Integer>`. Lets see how this could be done in Clojure using the `map` function.

    (ns temperature-converter-example.core)

    (defn fahrenheit-to-celsius [temp]
      (* (/ 5 9) (- temp 32)))

    (defn convert-to-celsius [coll]
      (map fahrenheit-to-celsius coll))

    (convert-to-celsius [32 50])
    => [0 10]

These two examples show one of biggest differences between the two paradigms. There's a lot less code in the Clojure example, because the iteration is done for you under the hood. This allows you to focus on just what you need to do to solve the problem. In this case, it's having a set of temperature data, and creating a function that converts the temperatures, then mapping the two together.

### Reduce

`reduce` is another high order function typically available in the functional toolbelt. As the name might imply, `reduce` works by reducing a collection down to a single value.

If I asked a Java developer, "How would you sum up all the numbers in a list?", without thinking, they will probably come up with something like this:

    public class ImperativeReduceExample {

        public int sum(int[] nums) {
            int sum = 0;

            for(int i = 0; i < nums.length; i++)
                sum += nums[i];

            return sum;
        }
    }

The `for` loop is a pretty standard way to iterate over a list in Java, there's nothing wrong with it. But how else would you accomplish this? It's easy, you can utilize a fundamental concept in Computer Science: recursion, which is just a function that calls itself. First, lets see how one can use the built in `reduce` function to accomplish this task.

    (ns reduce-example.core)

    (defn reduce-sum [total coll]
      (reduce + total coll))

    ;;(reduce-sum 0 [1 2 3 4 5])
    ;; => 15

As you see, the function passed into `reduce` is the `+` operator, a starting value, and a collection of numbers.  Under the hood, `reduce` recursively loops through all the elements in the given `coll` and applying the `+` to the total with each element until the collection is empty. Each time the function will call itself with the new sum and collection with an element removed. Sticking with the example above, let’s see how we would re-write the `reduce` function using recursion by hand.

    (ns reduce-example.core)

    (defn recursive-sum [total coll]
      (loop [sum total
             nums coll]
        (if (empty? nums)
          sum
          (recur (+ sum (first nums)) (rest nums)))))

    ;; (recursive-sum 0 [1 2 3 4 5])
    ;; => 15

If the loop doesn't have any items, it returns the sum. Otherwise, it re-calls the loop bindings `total` and `nums` with the new values. As you can see, the idea here is for your functions to simply take data and continually reduce your collection down to an eventual base case that will return the value of sum. Each of these examples only takes in what they need as parameters and does not rely on any outside state.

## So, Why Immutability?

I'm not exactly saying one is inherently better than the other; however, it is important to know when and where a functional language might be better suited. As we create more large scale applications that need to handle concurrent processes, functional languages will often make life a little bit easier. Also, recursion, and more specifically, tail-recursion with a `loop` in Clojure, will provide performance benefits since it does not consume stack memory space like Java does. The lack of side effects, immutable state, and focus on what to do instead of how to do it are just some of the reasons you may choose to use a functional language over an imperative one.

Lastly, remember that being a good programmer doesn't just involve knowing the solution to a problem. It is also about figuring out how to solve a problem from multiple approaches and choosing how to best accomplish your task. Naturally, as humans, it's too easy to continue solving problems in a way that we know will work. While you might find comfort sticking to what you already know, being a programmer requires you to continuously explore and learn new ways of thinking.
