Python
======

Exceptions
----------
- https://docs.python.org/3/library/exceptions.html#exception-hierarchy

### Common Exceptions
- AssertionError: qualifying tests in the code that fail, meaning something is wrong!
- AttributeError: Value of var has not been set, or the var does not exist
- IndexError: Raised when a sequence subscript is out of range. (Slice indices are silently truncated to fall in the allowed range; if an index is not an integer, TypeError is raised.)
- KeyError: Raised when a mapping (dictionary) key is not found in the set of existing keys.
- NameError: Raised when a local or global name is not found. This applies only to unqualified names. The associated value is an error message that includes the name that could not be found.
- NotImplementedError: In user defined base classes, abstract methods should raise this exception when they require derived classes to override the method, or while the class is being developed to indicate that the real implementation still needs to be added.
- TypeError: Raised when an operation or function is applied to an object of inappropriate type. The associated value is a string giving details about the type mismatch. Passing arguments of the wrong type (e.g. passing a list when an int is expected) should result in a TypeError, but passing arguments with the wrong value (e.g. a number outside expected boundaries) should result in a ValueError.
- ValueError: Raised when a built-in operation or function receives an argument that has the right type but an inappropriate value, and the situation is not described by a more precise exception such as IndexError.
