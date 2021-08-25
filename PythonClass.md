[https://docs.python.org/3/tutorial/classes.html](https://docs.python.org/3/tutorial/classes.html)

Namespaces are created at different moments and have different lifetimes. The namespace containing the built-in names is created when the Python interpreter starts up, and is never deleted.

The local namespace for a function is created when the function is called, and deleted when the function returns or raises an exception that is not handled within the function.

The global statement can be used to indicate that particular variables live in the global scope and should be rebound there; the nonlocal statement indicates that particular variables live in an enclosing scope and should be rebound there.

```
def scope_test():
    def do_local():
        spam = "local spam"

    def do_nonlocal():
        nonlocal spam
        spam = "nonlocal spam"

    def do_global():
        global spam
        spam = "global spam"

    spam = "test spam"
    do_local()
    print("After local assignment:", spam)
    do_nonlocal()
    print("After nonlocal assignment:", spam)
    do_global()
    print("After global assignment:", spam)

scope_test()
print("In global scope:", spam)
```
Output:
```
After local assignment: test spam
After nonlocal assignment: nonlocal spam
After global assignment: nonlocal spam
In global scope: global spam
```

# Class
When a class definition is entered, a new namespace is created, and used as the local scope
## built-in class attributes
1. `__doc__` returns the docstring belonging to the class
2. `__init__` is called when an object is created. It can customize instances to a specific initial state. It can also take arguments. It is similar to the constructor in java and C++. 
```
def __init__(self):
    self.data = []
```
**Note**: must use 'self.data' so the 'data' is in the namespace of the class. Without 'self', data's lifetime ends when `__init__` finishes.

3. the attribute fields, no matter created in `__init__` or not, are default to be public
4. `__class__` reflects the class of an instance

## methods
the special thing about methods is that the instance object is passed as the first argument of the function. In our example, the call x.f() is exactly equivalent to MyClass.f(x). In general, calling a method with a list of n arguments is equivalent to calling the corresponding function with an argument list that is created by inserting the method’s instance object before the first argument.

Generally speaking, instance variables are for data unique to each instance and class variables are for attributes and methods shared by all instances of the class.

When user calls instance.method(), the instance will be inserted to the front of other arguments (to replace `self`).

**Note.** Initialize attributes in i`__init__` if you want to make sure they are not shared across different instances. The following example is BAD because the 'mutable' data structure, list, can be modified by other instances.
```
class Dog:

    tricks = []    # mistaken use of a class variable

    def __init__(self, name):
        self.name = name

    def add_trick(self, trick):
        self.tricks.append(trick)

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks                # unexpectedly shared by all dogs
['roll over', 'play dead']
```
# Inheritance
Different from java, most built-in classes can be extended, e.g., list, tuple.

 