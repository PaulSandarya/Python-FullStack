# Top 100 Python Full-Stack Developer Interview Questions and Answers

A practical interview-preparation guide covering Python, programming
fundamentals, object-oriented programming, databases, backend
frameworks, APIs, frontend basics, testing, deployment, security, and
Git.

> **How to use this guide:** Try answering each question aloud before
> reading the answer. For coding questions, practise writing the
> solution without copying it.

## 1. Python Fundamentals

### 1. What is Python?

Python is a high-level, interpreted, general-purpose programming
language. It is known for readable syntax and a large ecosystem of
libraries. It is widely used in web development, automation, data
science, scripting, and AI.

### 2. What are the main features of Python?

-   Simple, readable syntax
-   Dynamically typed
-   Interpreted execution
-   Object-oriented and supports other programming styles
-   Cross-platform
-   Large standard library and third-party ecosystem
-   Automatic memory management

### 3. What is the difference between a list and a tuple?

A list is mutable, so its elements can be added, removed, or changed. A
tuple is immutable after creation. Lists use `[]`; tuples commonly use
`()`.

``` python
numbers = [1, 2, 3]
numbers[0] = 10

coordinates = (10, 20)
# coordinates[0] = 5  # TypeError
```

### 4. What is the difference between a set and a dictionary?

A set stores unique values without mapping keys to values. A dictionary
stores key-value pairs, with unique keys.

``` python
unique_ids = {1, 2, 3}
user = {"name": "Asha", "role": "Developer"}
```

### 5. What are mutable and immutable data types?

Mutable objects can be changed after creation; examples include lists,
dictionaries, and sets. Immutable objects cannot be changed in place;
examples include integers, floats, strings, and tuples (although a tuple
may contain a mutable object).

### 6. What is the difference between `==` and `is`?

`==` compares values for equality. `is` checks whether two references
point to the same object.

``` python
a = [1, 2]
b = [1, 2]
print(a == b)  # True
print(a is b)  # False
```

### 7. What are Python's built-in data types?

Common built-in types include `int`, `float`, `complex`, `bool`, `str`,
`list`, `tuple`, `range`, `dict`, `set`, `frozenset`, `bytes`,
`bytearray`, and `NoneType`.

### 8. What is type casting?

Type casting converts a value from one type to another, explicitly or
implicitly.

``` python
age_text = "25"
age = int(age_text)
price = float("99.5")
```

Conversion can fail when the input is not valid for the target type,
such as `int("hello")`.

### 9. What is the difference between `break`, `continue`, and `pass`?

-   `break` exits the nearest loop.
-   `continue` skips the rest of the current iteration and moves to the
    next one.
-   `pass` is a no-operation statement used as a placeholder.

### 10. What are `*args` and `**kwargs`?

`*args` collects extra positional arguments into a tuple. `**kwargs`
collects extra keyword arguments into a dictionary.

``` python
def show_details(*args, **kwargs):
    print(args)
    print(kwargs)

show_details(10, 20, name="Asha", city="Pune")
```

### 11. What is the difference between `append()` and `extend()`?

`append()` adds one object as a single element. `extend()` iterates over
an iterable and adds each of its elements.

``` python
items = [1, 2]
items.append([3, 4])  # [1, 2, [3, 4]]

items = [1, 2]
items.extend([3, 4])  # [1, 2, 3, 4]
```

### 12. What is list comprehension?

List comprehension is a concise way to create a list from an iterable,
optionally applying a condition.

``` python
squares = [n * n for n in range(1, 6)]
even_squares = [n * n for n in range(1, 6) if n % 2 == 0]
```

### 13. What is a lambda function?

A lambda is a small anonymous function consisting of a single
expression.

``` python
square = lambda x: x * x
print(square(5))  # 25
```

For complex logic, a regular `def` function is generally clearer.

### 14. What is the difference between `map()`, `filter()`, and `reduce()`?

-   `map()` applies a function to every item.
-   `filter()` keeps items that satisfy a condition.
-   `reduce()` combines items into one result; it is available from
    `functools`.

``` python
from functools import reduce

values = [1, 2, 3, 4]
doubled = list(map(lambda x: x * 2, values))
evens = list(filter(lambda x: x % 2 == 0, values))
total = reduce(lambda a, b: a + b, values)
```

### 15. What is a Python module and a package?

A module is a Python file that contains reusable code. A package
organizes related modules in a directory; traditional packages contain
an `__init__.py` file, though namespace packages can work without one.

### 16. What is the purpose of `if __name__ == "__main__":`?

It ensures that a block runs when the file is executed directly, but not
when the file is imported as a module.

``` python
def main():
    print("Application started")

if __name__ == "__main__":
    main()
```

### 17. What is exception handling in Python?

Exception handling manages runtime errors using `try`, `except`, `else`,
and `finally`. It prevents expected failures from crashing an
application unexpectedly.

``` python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
finally:
    print("Operation completed")
```

### 18. How do you create a custom exception?

Create a class that inherits from `Exception`, then raise it when a
domain-specific error occurs.

``` python
class InsufficientBalanceError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientBalanceError("Insufficient balance")
    return balance - amount
```

### 19. What is a decorator?

A decorator is a callable that wraps another function or class to add or
modify behaviour without changing the original implementation.

``` python
from functools import wraps

def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_call
def greet():
    return "Hello"
```

### 20. What is a generator?

A generator produces values lazily, one at a time, using `yield`. It can
reduce memory use when processing large data streams.

``` python
def count_up_to(limit):
    for number in range(1, limit + 1):
        yield number

for value in count_up_to(3):
    print(value)
```

## 2. Object-Oriented Programming

### 21. What is object-oriented programming (OOP)?

OOP structures software around objects that combine data (attributes)
and behaviour (methods). Its core concepts are encapsulation,
abstraction, inheritance, and polymorphism.

### 22. What is a class and an object?

A class is a blueprint defining attributes and methods. An object is an
instance of that class.

``` python
class Student:
    def __init__(self, name):
        self.name = name

student = Student("Asha")
```

### 23. What is `self` in Python?

`self` refers to the current instance of a class. It is the conventional
first parameter of instance methods and is used to access instance
attributes and methods.

### 24. What is the purpose of `__init__()`?

`__init__()` initializes an object's state after it has been created. It
is commonly called the constructor in everyday Python discussion,
although technically `__new__()` creates the object.

### 25. Explain the four pillars of OOP.

-   **Encapsulation:** Bundling data and methods and controlling access
    to internal state.
-   **Abstraction:** Exposing essential behaviour while hiding
    implementation details.
-   **Inheritance:** Deriving a class from another class to reuse or
    specialize behaviour.
-   **Polymorphism:** Allowing different object types to respond to the
    same interface.

### 26. What is inheritance? Name its types.

Inheritance lets a child class reuse or extend a parent class. Common
forms include single, multiple, multilevel, hierarchical, and hybrid
inheritance.

``` python
class Animal:
    def speak(self):
        return "Sound"

class Dog(Animal):
    def speak(self):
        return "Bark"
```

### 27. What is method overriding?

Method overriding occurs when a child class provides its own
implementation of a method inherited from a parent class. The child
implementation is used for instances of the child class.

### 28. Does Python support method overloading?

Python does not support traditional compile-time method overloading by
defining multiple methods with the same name. Similar behaviour can be
achieved using default arguments, `*args`, `**kwargs`, or dispatch tools
such as `functools.singledispatch`.

### 29. What is encapsulation in Python?

Encapsulation keeps related state and behaviour together and provides a
controlled interface. Python uses naming conventions such as `_name` for
non-public attributes and name mangling for `__name`; these are not
strict access-control mechanisms.

### 30. What is abstraction?

Abstraction defines what an object should do without requiring callers
to know how it does it. Python's `abc` module supports abstract base
classes and abstract methods.

``` python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass
```

### 31. What is polymorphism?

Polymorphism means different types can be used through a common
interface. For example, unrelated classes can both implement a `save()`
method and be used by code that expects that behaviour.

### 32. What is the difference between instance, class, and static methods?

-   Instance methods receive `self` and work with an instance.
-   Class methods receive `cls` and work with class-level state or
    alternate constructors.
-   Static methods receive neither automatically and are utility
    functions grouped inside a class.

``` python
class Employee:
    company = "Example Ltd"

    def __init__(self, name):
        self.name = name

    def describe(self):
        return self.name

    @classmethod
    def company_name(cls):
        return cls.company

    @staticmethod
    def is_valid_name(name):
        return bool(name.strip())
```

### 33. What is multiple inheritance?

Multiple inheritance allows a class to inherit from more than one parent
class. Python resolves method lookup using the Method Resolution Order
(MRO).

### 34. What is MRO?

MRO is the order Python follows when searching classes for an attribute
or method. You can inspect it with `ClassName.mro()` or
`ClassName.__mro__`.

### 35. What are magic or dunder methods?

These are special methods whose names begin and end with double
underscores. They let custom objects integrate with Python operations,
for example `__init__`, `__str__`, `__len__`, and `__eq__`.

## 3. Data Structures, Algorithms, and Core Concepts

### 36. What is the difference between a shallow copy and a deep copy?

A shallow copy creates a new outer object but keeps references to nested
objects. A deep copy recursively copies nested objects too.

``` python
import copy

original = [[1, 2], [3, 4]]
shallow = copy.copy(original)
deep = copy.deepcopy(original)
```

### 37. What is the difference between an iterable and an iterator?

An iterable can return an iterator, usually through `iter()`. An
iterator produces the next value with `next()` and remembers its
iteration state.

### 38. What is the Global Interpreter Lock (GIL)?

In the standard CPython build, the GIL historically allows only one
thread at a time to execute Python bytecode in a given interpreter.
Threads can still help with I/O-bound work. Python versions and builds
may offer different execution modes, so check the runtime being used.

### 39. What is the difference between multithreading and multiprocessing?

Multithreading runs multiple threads within one process and is often
useful for I/O-bound tasks. Multiprocessing uses separate processes and
can run CPU-bound work in parallel, at the cost of more memory and
communication overhead.

### 40. What is time complexity?

Time complexity describes how an algorithm's running time grows with
input size. Big-O notation expresses an upper-bound growth rate, such as
`O(1)`, `O(log n)`, `O(n)`, or `O(n²)`.

### 41. What is the difference between a stack and a queue?

A stack follows LIFO (last in, first out), while a queue follows FIFO
(first in, first out). Python lists can implement stacks;
`collections.deque` is often suitable for queues.

### 42. How do you reverse a string in Python?

Use slicing with a step of `-1`.

``` python
text = "python"
reversed_text = text[::-1]
print(reversed_text)  # nohtyp
```

### 43. How do you check whether a string is a palindrome?

Normalize the string as required, then compare it with its reverse.

``` python
def is_palindrome(value):
    cleaned = "".join(ch.lower() for ch in value if ch.isalnum())
    return cleaned == cleaned[::-1]

print(is_palindrome("Madam"))  # True
```

### 44. How do you find duplicates in a list?

Track values already seen in a set. This approach is typically O(n) time
on average.

``` python
def find_duplicates(values):
    seen = set()
    duplicates = set()
    for value in values:
        if value in seen:
            duplicates.add(value)
        seen.add(value)
    return duplicates
```

### 45. How do you find the second-largest distinct number in a list?

Remove duplicates, sort the values, and select the second-last item. For
large inputs, a one-pass approach can be more efficient.

``` python
def second_largest(values):
    unique = sorted(set(values))
    if len(unique) < 2:
        raise ValueError("At least two distinct values are required")
    return unique[-2]
```

### 46. What is recursion?

Recursion is when a function calls itself to solve smaller versions of a
problem. It needs a base case to stop; deep recursion can hit Python's
recursion limit.

### 47. What is a dictionary's average lookup complexity?

Dictionary lookup, insertion, and deletion are generally O(1) on
average, though worst-case behaviour can be worse due to hash
collisions.

### 48. What is sorting, and how do you sort a list in Python?

Sorting arranges values in a defined order. `list.sort()` changes the
list in place; `sorted()` returns a new sorted list.

``` python
numbers = [4, 1, 3]
numbers.sort()
new_numbers = sorted(numbers, reverse=True)
```

## 4. SQL and Database Concepts

### 49. What is a database?

A database is an organized collection of data that can be stored,
queried, updated, and managed. Applications commonly use relational
databases such as PostgreSQL and MySQL or NoSQL databases such as
MongoDB.

### 50. What is the difference between SQL and NoSQL databases?

Relational SQL databases typically use tables, defined schemas, and
relationships. NoSQL databases use models such as documents, key-value
pairs, wide columns, or graphs and may offer more flexible data
structures.

### 51. What is a primary key?

A primary key uniquely identifies each row in a table. It must be unique
and cannot contain `NULL`.

### 52. What is a foreign key?

A foreign key is a column or set of columns that references a candidate
key, commonly a primary key, in another table. It helps enforce
referential integrity.

### 53. What are the different types of SQL joins?

-   `INNER JOIN`: rows with matching values in both tables.
-   `LEFT JOIN`: all rows from the left table and matching rows from the
    right.
-   `RIGHT JOIN`: all rows from the right table and matching rows from
    the left.
-   `FULL OUTER JOIN`: all rows from both tables, matched where
    possible.

### 54. What is the difference between `WHERE` and `HAVING`?

`WHERE` filters rows before grouping. `HAVING` filters groups after
`GROUP BY` and is commonly used with aggregate conditions.

``` sql
SELECT department, COUNT(*) AS employee_count
FROM employees
WHERE active = TRUE
GROUP BY department
HAVING COUNT(*) > 5;
```

### 55. What is normalization?

Normalization organizes relational data to reduce unnecessary
duplication and update anomalies. Common normal forms include 1NF, 2NF,
and 3NF.

### 56. What is denormalization?

Denormalization intentionally adds redundancy or precomputed data to
improve read performance or simplify queries. It can make writes and
consistency management more complex.

### 57. What is an index in SQL?

An index is a data structure that helps the database find rows faster.
Indexes can improve reads but consume storage and may slow inserts,
updates, and deletes.

### 58. What is a database transaction?

A transaction is a logical unit of work that should be completed fully
or rolled back. ACID describes atomicity, consistency, isolation, and
durability.

### 59. What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?

-   `DELETE` removes selected rows and can use a `WHERE` clause.
-   `TRUNCATE` removes all rows from a table, with behaviour depending
    on the database.
-   `DROP` removes the database object itself, such as a table.

### 60. What is SQL injection, and how can you prevent it?

SQL injection occurs when untrusted input changes the meaning of a SQL
statement. Use parameterized queries or an ORM, validate inputs, and
avoid building SQL by concatenating user-provided strings.

``` python
cursor.execute(
    "SELECT * FROM users WHERE email = %s",
    (email,)
)
```

### 61. What is the difference between `COUNT(*)` and `COUNT(column)`?

`COUNT(*)` counts rows. `COUNT(column)` counts only rows where that
column is not `NULL`.

### 62. What is a view?

A view is a named query that behaves like a virtual table. It can
simplify repeated queries and restrict which columns or rows users see;
a normal view does not necessarily store its results.

## 5. Backend Development: Django, Flask, and FastAPI

### 63. What is backend development?

Backend development handles server-side logic, business rules,
authentication, data access, integrations, and APIs. It communicates
with clients such as browsers and mobile applications.

### 64. What is Django?

Django is a high-level Python web framework that includes an ORM, URL
routing, templates, authentication, an admin interface, and security
features. It follows a model-template-view (MTV) architectural pattern.

### 65. What is Flask?

Flask is a lightweight Python web framework. It provides core routing
and request/response functionality while allowing developers to choose
extensions and project architecture.

### 66. What is FastAPI?

FastAPI is a Python framework for building APIs. It uses Python type
hints, supports request validation through Pydantic, and generates
OpenAPI documentation.

### 67. What is the difference between Django, Flask, and FastAPI?

Django is feature-rich and suitable for larger applications that benefit
from built-in components. Flask is minimal and flexible. FastAPI is
especially useful for typed, high-performance API services and automatic
API documentation.

### 68. What is Django's MVT architecture?

-   **Model:** Defines data and database interactions.
-   **View:** Handles requests, application logic, and responses.
-   **Template:** Defines how presentation is rendered.

### 69. What is Django ORM?

Django ORM lets developers query and modify database records using
Python objects and query methods rather than writing SQL for every
operation.

``` python
# Retrieve active users
users = User.objects.filter(is_active=True)

# Create a record
User.objects.create(username="asha")
```

### 70. What are Django migrations?

Migrations are version-controlled instructions that apply model/schema
changes to a database. Common commands are
`python manage.py makemigrations` and `python manage.py migrate`.

### 71. What is middleware in Django?

Middleware is a layer that processes requests and responses globally. It
can handle concerns such as authentication, sessions, security headers,
and request logging.

### 72. What is the Django admin panel?

Django admin is an automatically generated interface for managing
registered models. It is useful for trusted internal users and can be
customized through `ModelAdmin`.

### 73. What is a Django QuerySet?

A QuerySet represents a collection of database queries. It is usually
lazy: the query is generally executed when the results are evaluated,
for example by iteration or conversion to a list.

### 74. What is the N+1 query problem?

The N+1 problem occurs when an application runs one query to fetch a
list and then an additional query for each item to fetch related data.
In Django, `select_related()` and `prefetch_related()` can reduce these
extra queries.

### 75. What is the difference between Flask and Django routing?

Both frameworks map URLs to handlers. Django commonly declares URL
patterns in URL configuration files; Flask commonly uses route
decorators such as `@app.route()`.

### 76. What is Pydantic?

Pydantic is a Python library for parsing and validating data using type
annotations. It is commonly used by FastAPI to validate request bodies
and serialize response data.

### 77. What is an environment variable, and why is it used?

An environment variable stores configuration outside source code, such
as database URLs, secret keys, and deployment settings. It helps
separate configuration from application code; secrets should be stored
in a secure secret manager in production.

## 6. REST APIs and Web Concepts

### 78. What is an API?

An Application Programming Interface (API) defines how software
components communicate. A web API commonly exposes endpoints that
clients access over HTTP.

### 79. What is a REST API?

A REST-style API exposes resources through URLs and uses HTTP methods to
perform operations. Good REST API design uses consistent resource
naming, appropriate status codes, and stateless requests.

### 80. Explain common HTTP methods.

-   `GET`: retrieve a resource.
-   `POST`: submit data, often to create a resource or trigger an
    operation.
-   `PUT`: replace a resource.
-   `PATCH`: partially update a resource.
-   `DELETE`: remove a resource.

### 81. What are common HTTP status codes?

-   `200 OK`: request succeeded.
-   `201 Created`: resource created.
-   `204 No Content`: succeeded without a response body.
-   `400 Bad Request`: invalid request.
-   `401 Unauthorized`: authentication is missing or invalid.
-   `403 Forbidden`: caller is not allowed.
-   `404 Not Found`: resource not found.
-   `409 Conflict`: request conflicts with current state.
-   `500 Internal Server Error`: unexpected server failure.

### 82. What is JSON?

JSON (JavaScript Object Notation) is a lightweight text format for
exchanging structured data. It supports objects, arrays, strings,
numbers, booleans, and `null`.

``` json
{
  "name": "Asha",
  "active": true,
  "skills": ["Python", "SQL"]
}
```

### 83. What is the difference between authentication and authorization?

Authentication verifies who a user is. Authorization determines what an
authenticated user is allowed to do.

### 84. What is JWT?

JSON Web Token is a compact token format used to transmit claims between
parties. A signed JWT can be verified by the server, but its payload is
normally readable and should not contain secrets. Use HTTPS and validate
expiry, issuer, audience, and signature.

### 85. What is CORS?

Cross-Origin Resource Sharing is a browser security mechanism that
controls whether a web page from one origin can access resources from
another origin. The server uses CORS response headers to specify
permitted origins, methods, and headers.

### 86. What is pagination in an API?

Pagination divides a large result set into smaller responses. Common
approaches include page/limit, offset/limit, and cursor-based
pagination. Cursor pagination can work better for large or frequently
changing datasets.

### 87. What is rate limiting?

Rate limiting restricts how many requests a client can make in a time
period. It helps protect services from abuse, accidental overload, and
some denial-of-service patterns.

## 7. Frontend Development

### 88. What is the difference between HTML, CSS, and JavaScript?

-   HTML defines page structure and content.
-   CSS controls presentation and layout.
-   JavaScript adds behaviour and interactivity.

### 89. What is semantic HTML?

Semantic HTML uses elements that describe their meaning, such as
`<header>`, `<nav>`, `<main>`, `<article>`, and `<footer>`. It improves
accessibility, maintainability, and document structure.

### 90. What is the CSS box model?

The CSS box model describes how an element's size is calculated from
content, padding, border, and margin. With `box-sizing: border-box`,
declared width and height include padding and border.

### 91. What is the difference between Flexbox and CSS Grid?

Flexbox is primarily designed for one-dimensional layouts (a row or
column). CSS Grid is designed for two-dimensional layouts with rows and
columns. They can be used together.

### 92. What is responsive web design?

Responsive design makes a website adapt to different screen sizes and
devices. Common techniques include fluid layouts, flexible images, CSS
media queries, and responsive units.

### 93. What is the DOM?

The Document Object Model is a programming representation of an HTML
document as a tree of nodes. JavaScript can use the DOM API to read and
update page content and structure.

### 94. What is the difference between `var`, `let`, and `const` in JavaScript?

`var` is function-scoped and can be redeclared. `let` and `const` are
block-scoped. `let` can be reassigned; `const` cannot be reassigned,
although an object or array declared with `const` can still be mutated.

### 95. What is the difference between localStorage, sessionStorage, and cookies?

`localStorage` persists until cleared. `sessionStorage` is scoped to a
browser tab session. Cookies can be sent automatically with HTTP
requests and support attributes such as `HttpOnly`, `Secure`, and
`SameSite`; their size and lifetime are limited.

## 8. Testing, Deployment, Security, and Git

### 96. What is unit testing?

Unit testing checks a small unit of code, such as a function or method,
in isolation. Python's built-in `unittest` and third-party `pytest` are
commonly used testing frameworks.

``` python
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
```

### 97. What is the difference between unit, integration, and end-to-end testing?

-   **Unit testing:** tests a small component in isolation.
-   **Integration testing:** tests whether multiple components work
    together.
-   **End-to-end testing:** tests a complete user workflow through the
    application.

### 98. What is Docker?

Docker packages an application and its dependencies into a container
image. Containers help applications run consistently across development,
testing, and production environments.

### 99. What is CI/CD?

Continuous Integration (CI) automatically builds and tests code changes.
Continuous Delivery/Deployment (CD) automates preparing or releasing
those changes to an environment. Pipelines commonly run linting, tests,
builds, and deployment steps.

### 100. What are Git and GitHub, and what is a typical workflow?

Git is a distributed version-control system. GitHub is a hosting and
collaboration platform for Git repositories. A typical workflow is to
clone a repository, create a branch, make changes, commit, push, and
open a pull request.

