## Python data types
Python has the following data types **built-in** by default, in these categories:

* Text Type:	str
* Numeric Types:	int, float, complex
* Sequence Types:	list, tuple, range
* Mapping Type:	dict
* Set Types:	set, frozenset
* Boolean Type:	bool
* Binary Types:	bytes, bytearray, memoryview

In Python, the data type is automatically set when you assign a value to a variable. You can also specify a datetype when initializing a variable: `x = float(20)`, `x = bool(5)`

### Immutable vs Mutable
Immutable: int, float, bool, **string**, unicode, **tuple**.

Mutable: list, dict, set 


## String
Like many other popular programming languages, strings in Python are arrays of bytes representing unicode characters. 

However, Python does not have a character data type, a single character is simply a string with a length of 1.

In many cases we can use string as array:
* Accuess element `string[1]`
* Slicing `string[2:5]`
* Length `len(string)`

Other than that, `str` has its unique built-in methods:
* .strip() removes any whitespace from the beginning or the end
* .lower() and .upper() change to lower and upper cases
* .split(",") splits a string into a list
* .replace('a', 'b') replace a string by another
* `if "abc" in "abcde"` to check if a substring exist

Note: Since string is an immutable datatype, calling the method, e.g. a.strip(), won't change string a, but to return a new string.

Question: For leetcode that need to modify a string, how to do it efficiently？May be use array of length-1 strings?

## References
[https://www.w3schools.com/python/python_datatypes.asp](url)