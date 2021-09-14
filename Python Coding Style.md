PEP 8 coding style https://www.python.org/dev/peps/pep-0008/#code-lay-out

1. Comparisons to singletons like None should always be done with `is` or `is not`, never the equality operators.

   Be careful using ``if x`. Consider of using `if x is not None` because some type may be false in a boolean context

2. Always use a def statement instead of an assignment statement that binds a lambda expression directly to an identifier:

   ```
   # Correct:
   def f(x): return 2*x
   # Wrong:
   f = lambda x: 2*x
   ```

   The first form means that the name of the resulting function object is specifically 'f' instead of the generic '<lambda>'. This is more useful for tracebacks and string representations in general. The use of the assignment statement eliminates the sole benefit a lambda expression can offer over an explicit def statement (i.e. that it can be embedded inside a larger expression)

3. Use `''.startswith()` and `''.endswith()` instead of string slicing to check for prefixes or suffixes.

   startswith() and endswith() are cleaner and less error prone:

   ```
   # Correct:
   if foo.startswith('bar'):
   # Wrong:
   if foo[:3] == 'bar':
   ```

4. Object type comparisons should always use isinstance() instead of comparing types directly:

   ```
   # Correct:
   if isinstance(obj, int):
   # Wrong:
   if type(obj) is type(1):
   ```

5. for sequences, (strings, lists, tuples), use the fact that empty sequences are false:

   ```
   # Correct:
   if not seq:
   if seq:
   # Wrong:
   if len(seq):
   if not len(seq):
   ```

6. Don't compare boolean values to True or False using `==`:

   ```
   # Correct:
   if greeting:
   # Wrong:
   if greeting == True:
   if greeting is True:
   ```

7. Imports should usually be on separate lines:

   ```
   # Correct:
   import os
   import sys
   # Wrong:
   import sys, os
   ```

   It's okay to say this though:

   ```
   # Correct:
   from subprocess import Popen, PIPE
   ```