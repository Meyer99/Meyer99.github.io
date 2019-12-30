---
title: Python Basics (Part 1)
subtitle: Brief Summary of Python Crash Course by Eric Matthes (Chapter 1 ~ 6)
layout: post
mathjax: false
author: Meyer
catalog: true
header-img: "img/python.jpg"
tags: 
    - Python
---

This article records some basic Python operations following the book *Python Crash Course* and is mainly for reference to Python grammar.

### Chapter 1: Getting Started
* It's a good practice to name the file and folder with lower case letters and use underscore to represent space because it's Python's naming convention.

### Chapter 2: Variables and Simple Data Types
* It's better to name variables with lower case letters by convention.
* String operations (note that the following methods do not affect the original string):
  * `str.upper()`
  * `str.lower()`
  * `str.title()`: the first letter of each word is capitalized
  * concatenation: `+`
  * `str.lstrip()`
  * `str.rstrip()`
  * `str.strip()` = `lstrip()` + `rstrip()`
* The `print` function: `print(value, sep=' ', end='\n')`
  * `sep`: separator inserted between `value`
  * `end`: cursor is moved to a new line by default
* Multi-line string:
  ```python
  s = "this is a very " \
      "long string too " \
      "for sure ..."
  ```
  ```python
  s = ("this is a very "
      "long string too "
      "for sure ..."
      )
  ```
  Both result in `'this is a very long string too for sure ...'`
* Number can not be concatenated with string, use `str()` to cast
* Single-line comment: begin with `#`  
  Multi-line comment: enclosed in triple single/double quotation mark

### Chapter 3: Introducing Lists
* Use square brackets `[]` to represent list and comma to separate elements in the list. The elements do not necessarily be the same type.
* 0-indexed; negative index counts from the end of the list
* Add element:
  * `lst.append()`
  * `lst.insert(index, value)`
* Delete element:
  * delete by index: `del lst[index]`
  * delete last element and continue to use this value: `popped = lst.pop()`
  * delete by index and continue to use this value: `popped = lst.pop(index)`
  * delete by value and continue to ues this value: `removed = lst.remove(value)`  
    (Note that only the first element with `value` is deleted)
* Sort list:
  * Use `sort()` to sort list permanently: `lst.sort()` or `lst.sort(reverse=True)`
  * Use `sorted()` to sort list temporarily: `sorted(lst)` or `sorted(lst, reverse=True)`
* Reverse list permanently: `lst.reverse()`
* Get the length of the list: `len(lst)`

### Chapter 4: Working with Lists
* `for` loop to loop through the list:
  ```python
  for element in list:
      <do something>
  ```
  (be careful with indentation)
* The `range()` function:
  * `range(stop)` generates integers in the range `[0, stop)` with `step` 1
  * `range(start, stop, step)` generates integers in the range `[start, stop)` with `step` (default `step` is 1)
* Number list:
  * Use `list()` to convert `range` to list
  * Simple operations: `min(lst)`, `max(lst)`, `sum(lst)`
* List comprehension: [expression `for` item `in` list `if` condition]  
  e.g. `[value ** 2 for value in range(120) if value <= 10 and value > 0]` results in list `[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]`
* Slice: `list[start:stop:step]`, `stop` is not included  
  ```python
  lst[:]      # a copy of the whole list
  lst[::-1]   # all elements in the list, reversed
  ```
* To copy a list, use `lst.copy()` or `a = b[:]` to avoid making two different variables refers to the same list
* Tuple: an immutable list; use `()` to represent; trying to modify the element in tuple will result in an error

### Chapter 5: if Statements
* `if` statement syntax:  
  ```python
  if condition:
      <do something>
  ```
* Test for equality: `==` / `!=`
* Multiple conditions: combined with keyword `and` / `or`
* Test for presence in list: `in` / `not in`
* `if-else` statement:
  ```python
  if condition:
      <do something>
  else:
      <do something>
  ```
* `if-elif-else` structure: multiple `elif` statements can be used, `else` statement can be omitted
* Test for empty list: `if lst:` evaluates to `True` if `lst` is not empty

### Chapter 6: Dictionaries
* Use braces `{}` to represent dictionary, which contains *key-value* pair (`key:value`)
* Add new key-value pair: `dict[key] = value` (Note that elements in the dictionary are not in the order they were added)
* Delete key-value pair permanently: `del dict[key]`
* Iterate through the dictioinary:
  * keys and values: `for k, v in dict.items()`
  * keys: `for k in dict.keys()` (which returns a list) / `for k in dict`
  * values: `for v in dict.values()` (can be used in conjunction with `set()` to eliminate duplicates)
* Dictionaries and lists can be nested within each other
