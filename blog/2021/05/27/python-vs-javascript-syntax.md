---
title: Python Syntax Hot Take from a JavaScript Perspective
description:
date: 2021-05-27
readingTime: 5 minutes
tags: ["javascript", "python"]
---

{% image "./blog/2021/05/27/montyPython.jpeg", "Knights from Monty Python movie", "(max-width: 640px) 100%, 100%", page.url %}

Below are the high-level syntax and data type differences from JavaScript I noted while learning Python basics. If you're reading this it assumes you already know JavaScript. I'm not going deep here but this should serve as a cheat sheet to get acquainted with Python syntax.

## Basics

Python has class-based inheritance as opposed to JavaScript's prototypal inheritance.

Both languages are dynamically typed but Python has strong typing and JavaScript has weak typing. Maybe the biggest takeaway here is that JavaScript tries really hard to implicitly convert incompatible types behind the scenes. Python does not and will raise exceptions instead.

In Python, the indentation of lines is used to group code blocks. You won't find many curly braces or semi-colons in Python code. Make sure you use spaces and not tabs. The official style guide dictates indentation of 4 spaces, however, some big companies use 2. Setup your editor accordingly.

In JavaScript, you use the keywords ~~`var`~~, `let`, and `const` to declare a variable or constant. In Python there is none of that, just name the variable all by itself.

Your trusty friend `console.log()` is now called `print()`.

The `len()` function will tell you the length of objects.

The `type()` function will tell you the type of objects.

```py
my_string = 'string'
len(my_string) # => 6
len([0, 1, 2, 3, 4]) # => 5
type('string') # => str
type([0, 1, 2, 3, 4]) # => list
type(1) # => int
type(3.14) # => float
```

Notice above that Python has distinct integer and float types.

There are no `++` or `--` operators but you can use `+=` and `-=`.

Dividing with the `/` operator will return a floating-point number. Use the `//` operator to return whole numbers (rounded down).

The JavaScript string and array `slice(beginIndex, endIndex)` method is just `[beginIndex:endIndex]` in Python for strings and lists. If you omit either index it defaults to the beginning or end respectively. Omitting both is an idiomatic way to make a copy of a list in Python.

```py
my_string = 'Hello World'
my_string[1:5] # => 'ello'
my_string[:5] # => 'Hello'

my_list = [0, 1, 2, 3, 4]
my_list[-3:] # => [2, 3, 4]
my_list[-2] # => [3]

my_list_copy = my_list[:]
print(my_list_copy) # => [0, 1, 2, 3, 4]
```

Concatenating strings and other object types with the `+` operator does not perform implicit type conversions like JavaScript. If you want to concatenate a string and a number you'll need to explicitly convert the number to a string first with the `str()` function.

There are a bunch of ways to put strings together in Python. F-strings (last example) are the newest and easiest option. It's the equivalent of a JavaScript template literal.

```py
name = 'Arnold'
numero_uno = 1
"""
Using the modulo with a tuple.
The letter following the modulo needs to match the type of the object being inserted.
%s denotes a string. %i or %d for an integer. There are others you can look up for floats, etc.
"""
print('%s is number %i' % (name, numero_uno)) # => Arnold is number 1

# Using the modulo with a dictionary. Note the trailing "s" and "d"
print("%(name)s is numero %(number)d" % {"name": "Arnold", "number": 1})

# Convert the number to a string before concatenating.
print(name + ' is number ' + str(numero_uno))

# Use the .format() method
print('{} is number {}'.format(name, numero_uno))

# Wow... zero-index based positional and keyword references
print('{3} {2} {1} {heli}{0}'.format('!', 'the', 'to', 'Get', heli='choppa'))
# => Get to the choppa!

# Formatted "f-string" literal. "Replaces" the .format() method above.
# Closest to a JS template literal.
print(f'{name} is number {numero_uno}')
```

```js
// JavaScript example for comparison.
const name = "Arnold";
const numeroUno = 1;
// Template literal
console.log(`${name} is number ${numeroUno}`);

// JS converts the number to string for us when using the + operator with another string.
console.log(name + " is number " + numeroUno);
```

The previous example reminded me, apparently so-called "Pythonistas" prefer snake_case in most cases instead of camelCase. The name of the language is a Monty Python reference so what is with the obsession with snakes?

Also, did you notice the comments above? Use `#` instead of `//` and `"""` triple-quotes to enclose a multi-line comment.

Boolean operators are the words `and`, `or`, and `not` spelled out instead of `&&`, `||`, and `!`.

## Data Structures

Your best friend `Array` is now called `list`. You can concatenate lists by just "adding" them together with the `+` operator. You'll have to `append()` instead of `push()` but you can still `pop()`. You'll want to read up on the different methods.

Another data structure in Python is called a `tuple`. It is an immutable fixed-size list of elements. Technically, one of the contained elements could be mutable but the tuple itself is immutable. A tuple is declared with a comma-separated list of values inside of parentheses. They behave like lists in a lot of ways (zero-indexed, slicing, looping with for/in, bracket notation, etc.).

```py
this_is_a_tuple = (1, 2, 'three')
print(this_is_a_tuple[2]) # => three
this_is_a_tuple[0] = "ONE" # => TypeError: 'tuple' object does not support item assignment
```

A dictionary (type == `dict`) is an unordered list of key/value pairs wrapped in curly braces that maintains its element insertion order. It's kind of like a Map in JavaScript. Strings, numbers, and tuples can be used as keys. Any type can be a value. Use standard bracket notation for accessing/writing to properties.

- Looping over a dictionary with for/in iterates over its keys by default.
- Also has `keys()` and `values()` methods that return a list of the keys and values.
- `items()` returns a list of key/value tuples.

```py
my_dictionary = {'name': 'Alex', 'age': 100, 1: 'numero_uno'}
print(my_dictionary['name']) # => Alex

my_dictionary['age'] = 5
print(my_dictionary) # => {'name': 'Alex', 'age': 5, 1: 'numero_uno'}

print(my_dictionary.values()) # => dict_values(['Alex', 5, 'numero_uno'])

print(my_dictionary.items()) # => dict_items([('name', 'Alex'), ('age', 5), (1, 'numero_uno')])

for key, value in my_dictionary.items():
  print(f'Key: {key}  |  Value: {value}')
```

Python also has the Set type which is very similar to a JavaScript Set, just mentioning it here for completeness.

## Control Flow

`if` statements don't use parentheses or curly braces, only a single colon and line indentation. `elif` is the keyword for `else if`.

```py
if name == 'Arnold' and numero_uno == 1:
  print('%s is number %d' % (name, numero_uno))
elif name == 'Alex' or name == 'Lou':
  print('I\'m gonna beat \'em!')
else:
  print('Go back to the gym!')
```

Use `for...in...` to iterate through lists, dictionaries, strings, etc. as opposed to `for...of...` for JavaScript Arrays and iterable objects. In Python, you can also just use the `in` or `not in` keywords to test for membership. It returns True or False.

```py
list = ['Arnold', 'Franco', 'Lou', 'Ronnie']
for name in list:
  print(name)

if 'Ronnie' in list:
  print('Light weight baby!')
```

The `range()` function is useful to generate sequences of numbers to loop over.

```py
for i in range(10):
  print(i)
```

Python does not have a `switch` statement.

Python does have `while` loops.

## Functions

Functions are defined with the `def` keyword followed by the function name and a pair of parentheses and a colon. The parentheses should be empty if the function accepts no arguments or they can contain a named comma-separated parameter list. Optional parameters must have a default value assigned and must appear after any required parameters.

Python will raise an exception if you pass unexpected arguments to a function. This is much different than JavaScript which will allow you to call a function and pass just about anything.

```py
def hello():
  print('Hello World')

def greeting(name, message = 'welcome to my blog.'):
  print(f'Hello {name}, {message}')

greeting('Alex') # => Hello Alex, welcome to my blog.
greeting('Alex', 'please go away.') # => Hello Alex, please go away.
greeting('Alex', 'nice to meet you.', 'bye bye') # => TypeError: greeting() takes
# from 1 to 2 positional arguments but 3 were given
```

## Misc

Exception handling in Python uses `try/except[exceptionName]/finally` as opposed to `try/catch/finally` in JavaScript. The exception name needs to be a valid Python exception.

```py
def my_function():
  try:
      # Doing things
  except RuntimeError:
      print('Nope')
  else:
      # Success!
  finally:
      print('Thanks for stopping by!')
```

Global variables declared outside of functions can be referred to with no issue but if you want to write to a global variable from within a function you need to refer to it with the `global` keyword. Otherwise, you'll be creating a new local variable in the function scope.

```py
number = 1

def print_number():
    # Reads global variable with no problem
    print(number) # => 1

def change_number():
    global number
    # This will properly update the global variable
    number = 2
```

## Sources

[Google's Python Class](https://developers.google.com/edu/python)
[Learn Python in 10 minutes](https://www.stavros.io/tutorials/python/)
[Python Bootcamp - Udemy](https://www.udemy.com/course/complete-python-bootcamp/)
