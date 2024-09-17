---
layout: post
title:  "Python Tips - List and Dictionary Comprehension"
author: Brady Richardson
description: Learn some new python tricks to speed up your development and make your code cleaner.   
image: "/assets/images/snake-543243_1280.jpg"
---

# Tired of for-loops clunking up your code? It's time to simplify with comprehension
<div style="display: flex; justify-content: center">
  <img src="https://imgur.com/oXRwpxI" alt="" style="width:600px;"/> 
</div>

`Comprehension` is a technique in Python used to create data structures like lists and dictionaries in a simple, clean way. It might seem scary at first,
but it is easy to master once you've practiced it a few times. Put on your big boy/girl pants and let's dive in.

## List Comprehension

The first type of comprehension we are going to discuss is called `list comprehension`. The following two code snippets will demonstrate a scenario where list comprehension can help to simplify our code:

```python
my_list = []

for i in range(0, 110, 10):
  my_list.append(i)

print(my_list)

# this will print: [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```
Now, using a `for-loop` is a viable way to create a list, but it takes **multiple** lines of code to do what list comprehension can do in **one**.

Let's see how list comprehension can help us simplify this process:

```python
my_list = [i for i in range(0, 110, 10)]

print(my_list)

# this will print: [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```

If you noticed, our three lines of code became one thanks to this simple pythonic trick! Pretty neat! List comprehension can also get more advanced—for example, you can `apply functions` to each element as you iterate over them:

```python
def multiply_by_2(number):
  return number * 2

multiplied_list = [multiply_by_2(n) for n in range(10)]

print(multiplied_list)

# this will print: [0, 2, 4, 6, 8, 10, 12, 14, 16, 18], where each element from 0-9 has had the multiply_by_2 function applied to them
```

Or you can `conditionally filter` out elements as you iterate over them:

```python
conditional_list = [c for c in range(10) if c % 2 == 0 and c != 0]

print(conditional_list)

# this will print: [2, 4, 6, 8], where all odd numbers (and 0) have been filtered out
```

Another trick we'll go over is called `multiple comprehension`. Multiple comprehension allows us to  Here is what multiple comprehension looks like in code:

```python
my_list = [(x,y) for x in range(5) for y in range(3)]

print(my_list)

# this will print: [(0, 0),(0, 1),(0, 2),(1, 0),(1, 1),(1, 2),(2, 0),(2, 1),(2, 2),(3, 0),(3, 1),(3, 2),(4, 0),(4, 1),(4, 2)]
```

Multiple comprehension allows us to replace nested for-loops. The example above would be equivalent to using a double for-loop to iterate through two sets of numbers.

The last type of list comprehension we'll discuss is called `nested comprehension`. This is similar to multiple comprehension, but notice the key difference: we are using list comprehension **INSIDE** of list comprehension! Wow! Here is what nested comprehension looks like in practice:

```python
my_list = [[x**2 for x in range(10)] for y in range(5)]

print(my_list)

# this will print: [[0, 1, 4, 9, 16, 25, 36, 49, 64, 81],[0, 1, 4, 9, 16, 25, 36, 49, 64, 81],[0, 1, 4, 9, 16, 25, 36, 49, 64, 81],[0, 1, 4, 9, 16, 25, 36, 49, 64, 81],[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]]
```

Note that these examples are not an exhaustive list of everything that is possible with list comprehension, but they are meant to give you a quick foundation for learning this concept! Let's move on from list comprehension and check out its not-so-distant cousin, `dictionary comprehension`!

---

## Dictionary Comprehension

We aren't going to expend as much effort going over `dictionary comprehension` because hopefully you get the gist of what is possible through comprehension, and if your brain is still a little wiggly, then this is your invitation to go out and experiment to learn what is possible with dictionary (and list) comprehension yourself! That being said, here is a simple example of dictionary comprehension, see if you can decipher what is going on before looking at the commented result:

```python
random_data = [0, 3, 6, 9, 12, 15, 18, 21, 24, 27]

my_dict = {x:x**2 for x in random_data}

print(my_dict)

# this will print: {0: 0, 3: 9, 6: 36, 9: 81, 12: 144, 15: 225, 18: 324, 21: 441, 24: 576, 27: 729}
```

Did you figure it out? If not, don't worry, it takes time to learn these concepts. The best way to master these techniques is through practice!

Here is one more challenge for you:

```python
my_dict = {x:[y for y in ['A', 'B', 'C']] for x in range(6) if x % 2 != 0}

print(my_dict)
```

Take a second to make an educated guess before looking at the below result!

```python
# the result is: {1: ['A', 'B', 'C'], 3: ['A', 'B', 'C'], 5: ['A', 'B', 'C']}
```

**Congrats!** Now you know a little more about Python and are on your way to becoming a certified Python master. In the event that you want to learn a little more about list and dictionary comprehension, follow the links below!

### Read More 

Click here to learn more about list comprehension: [Geeks for Geeks: List Comprehension](https://www.geeksforgeeks.org/python-list-comprehension/)

Click here to learn more about dictionary comprehension: [Geeks for Geeks: Dictionary Comprehension](https://www.geeksforgeeks.org/python-dictionary-comprehension/)
