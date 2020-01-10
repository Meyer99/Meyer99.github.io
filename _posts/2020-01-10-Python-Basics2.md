---
title: Python Basics (Part 2)
subtitle: Brief Summary of Python Crash Course by Eric Matthes (Chapter 7 ~ 11)
layout: post
mathjax: false
author: Meyer
catalog: true
header-img: "img/python.jpg"
tags: 
    - Python
---

This article records some basic Python operations following the book *Python Crash Course* and is mainly for reference to Python grammar.


### Chapter 7: User Input and while Loops
* The function `input()` pauses the program and wait for the user to enter some text. It takes one parameter, i.e., the *prompt* displayed to the user. E.g.: 
```python
message = input("Type something: ")
```
* When using `input()`, the user input is stored as a string. `int()` can be used to convert it to a number.
* `while` loop: 
```python
while condition:
    <do something>
```
* Keywords: `break` and `continue`
* `Ctrl + C` can be used to stop the currently running program.


### Chapter 8: Functions
* Keyword: `def`
* The *docstring* followed by `def` describes what a function does and is enclosed by triple quotation marks.
* Arguments can be specified by:
  * position: `foo('arg1', arg2)` (pay attention to the order)
  * parameter's name: `foo(param1='arg1', param2=arg2)` (the order is not important)
  * default value: `def foo(param1, param2=arg2)`
  * mix of the above: note that parameters without default values should be listed first to enable correct interpretation of arguments specified by positions
* To make an argument optional, one can set its default value to the empty string `''` and perform corresponding test in the function body.
* Keyword: `return`
* When passing a list as an argument, any modifications to the list in the function are permanent. To avoid modifying the original list, one can use `function_name(list_name[:])` to pass a copy of the original list, but it takes time to create a copy.
* Passing arbitrary number of arguments:
  * `*args` encapsulates the arguments into a tuple
  * `**kwargs` encapsulates the key-value pairs into a dictionary
  * E.g., 
    ```python
    def foo(a, *b, **c):
        print(a)
        print(b)
        print(c)
    foo(1, 2, 3, x=4, y=5)

    ---Output:---
    1
    (2, 3)
    {'x': 4, 'y': 5}
    ```
* Import functions from module:
  * Modules are files with a `.py` extension
  * Import the entire module: `import module_name` and use `module_name.function_name()` to invoke functions
  * Import specific functions: `from module_name import function_1, function_2` and use the function's name directly 
  * `as` can be used to assign alias to a function or module
  * `*` can be used to import all functions from a module
* Suggested format for a function with many parameters:
  ```python
  def function_name(
          parameter_0, parameter_1, parameter_2,
          parameter_3, parameter_4, parameter_5):
      function body...
  ```

### Chapter 9: Classes
* A comprehensive example: 
    ```python
    class X():
        def __init__(self, a, b):
            self.a = a      # public
            self.__b = b    # private; renamed internally
            
        def print_b(self):
            print(self.__b)
            
    class Y(X):  # inheritance
        def __init__(self, a, b):
            super().__init__(a, b)
            self.c = 40
            
    x = X(1, 2)
    print(x.a)    # 1
    print(x.__b)  # 'X' object has no attribute '__b'

    y = Y(10, 20)
    print(y.a)    # 10
    # using self.__b in class Y will result in an error
    # 'Y' object has no attribute '_Y__b'
    y.print_b()   # 20
    ```

* Overwrite: subclass overwrites the method with the same name in its superclass
* Import classes:
  * `from module_name import Class1, Class2`
  * A module can store several classes 
  * Use `module_name.class_name` when an entire module is imported
  * `from module_name import *` imports all the classes in the module, but doing so is not recommended
* `OrderedDict` from module `collections` functions almost the same as a dictionary, except that it records the order of key-value pairs stored in it

### Chapter 10: Files and Exceptions
* Read files:
  * Open a file and automatically close it
  ```python
  with open(file_path) as file_object:
      <do something>
  ```
  * Read the entire file: `file_object.read()`
  * Read by lines: `for line in file_object` or `file_object.readlines()` (which returns a list)
  * When reading a text file, Python interprets all text in it as a string
  * Relative file path: relative to the directory where the program is currently running
  * Absolute file path: where the file is stored in the computer
  * Always use `\\` or add an `r` before the file path string when specifying the file path in Windows 
* Write files:
  * function `write()` can only write string into text files
    ```python
    with open(file_path, 'w') as file_object:
        file_object.write('Hello world!\n')
    ```
  * Mode:
    * `'r'`: Opens a file for reading, error if the file does not exist
    * `'r+'`: Opens a file for reading and writing (overwrite), error if the file does not exist
    * `'w'`: Opens a file for writing (overwrite), creates the file if it does not exist
    * `'w+'`: Opens a file for reading and writing (overwrite), creates the file if it does not exist
    * `'a'`: Opens a file for appending, creates the file if it does not exist
    * `'a+'`: Opens a file for reading and appending, creates the file if it does not exist
    * `'b'`: Binary mode
* Exceptions handling:
  * `try-except-else` block:
    ```python
    try:
        <do something>
    except ErrorName:
        <handle exception here>
    else:
        <execute when no exception occurs>
    ```
   * The codes that may raise an exception is placed in the `try`-block. `except`-block indicates what to do when an exception occurs. `else`-block is executed when no exception occurs in the `try`-block. Codes that depend on the successful execution of the `try`-block should be placed in the `else`-block. `else`-block is optional.
   * Use a `pass` statement in the `except`-block if you want to do nothing when an exception occurs
 * A simple example demonstrating the usage of `json`:
   ```python
   import json

   numbers = [2, 3, 5, 7, 11, 13]

   filename = 'numbers.json'
   with open(filename, 'w') as f_obj:
       json.dump(numbers, f_obj)  # use json.dump() to store

   with open(filename) as f_obj:
       nums = json.load(f_obj)    # use json.load() to retrieve
   print(nums)
   ```
* You can return `None` when the retrieval of something failed and use `if` to test if a variable is `None`

### Chapter 11: Testing Your Code
* The `unittest` testing framework provides a rich set of tools for constructing and running tests. It was originally inspired by JUnit and has a similar flavour as major unit testing frameworks in other languages.