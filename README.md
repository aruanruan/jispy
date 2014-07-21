# Jispy
Jispy is a recursive descent parser for a strict subset of Javascript.  
It is written in pure Python, distributed as a single file and highly extendable.

**Note:** This document and documents linked herein are work in progress.

The subset of JavaScript that Jispy interprets is fondly called [LittleJ](https://github.com/sumukhbarve/jispy/blob/master/LittleJ.md).  
Please at least skim it before reading further.

Jispy comes armed with a console and an API. While the API provides far greater control, the console is great for getting started.


### The `console()`
Let's jump right in:
```py
 >>> from jispy import console
 >>> console()
 jispy> var h = 'Hello World', arr = ['I', 'am', 'an', 'array'], obj = {}; 
 jispy> writeln(h);
 Hello World
 jispy> writeln(type(arr) + ' is not the same as ' + type(obj));
 array is not the same as object
 jispy> writeln(1 + 2 + 3 === 3 + 2 + 1);
 true
 jispy> writeln(1 + 2 + 3 != 1 + 1);
 SyntaxError: unexpected token !=
 jispy> writeln('bye!!');
 bye!!
```

Four functions come builtin with (the standard configuration of) Jispy:

+ `write()`, `writeln()`, `type()`, `keys()`, `str()`, `len()`.  
Each accepts a single argument. The last two are like their python-versions.  
`keys()` is equivalent to `Object.keys()` in JS or `dict.keys()` in Python.

To enter multiple lines of code, end each line with a tab.  
Here's an example with `for`:
```python
 >>> jispy.console()
 jispy> var obj = {a: 'apple', b: 'ball', c: 'cat'}, i = 0, k = 0;
 jispy> for (i = 0; i < len(obj); i += 1) {      
 ......     k = keys(obj)[i];    
 ......     writeln(k + ' for ' + obj[k]);       
 ...... }
 a for apple
 c for cat
 b for ball
```

And oh yes! Objects have `len()` in LittleJ.  
`len(obj)` equivalent to `Object.keys(obj).length` in JavaScript.

### The API

Consider the following LittleJ program for computing factorial:
```javascript
var n = 6, factorial = function (n) {
    if (n <= 1) { return 1; }
    return n * factorial(n - 1);
};
writeln(factorial(n)); // prints 720
```

Assuming that the above LittleJ program for computing factorial has been saved as a python-string in the variable `factoProg`, the following python code would execute it.
```python
 # ... define factoProgo ...
from jispy import Runtime
rt = Runtime()
rt.run(factoProgo); # Runtime().run(factoProgo) would have the same effect.
 # 720 will be printed
```

A `Runtime` is a wrapper around a global environment. It allows you to run multiple programs in the same environment. If you don't wish to do so, create a new instance of `Runtime` each time, or simply call `__init__` on the existing one.

`Runtime`'s constructor optionally accepts three arguments (in order):

+ `maxLoopTime`: The maximum time in seconds for which a loop may run.
+ `maxDepth`: The maximum allowable depth of nested environments (scopes).
+ `write`: `write` method of a `file`. It defaults to `sys.stdout.write`.

`maxLoopTime` and `maxDepth` both default to `None`. Unless changed to a positive value, there shall be no checks on infinite-looping and/or infinite-recursion.

if `write` is set to `None`, inbuilts `write` and `writeln` shall not be make available.

#### More about `console()`

Each call to Jispy's `console()` uses a **single** `Runtime`, wherein each input line (or lines) is executed as an independent program. But since the `Runtime` is common, later programs have access to variables defined in earlier programs.


### Adding Natives

Functions such as `writeln` may be added via `Runtime`'s `addNatives` method.
It accepts a single dictionary as input. The key-value pairs are added to the global environment.

For example, let's add a native version of the `factorial` function and set native `inbuilt_num` to `7.0` :
```python
from jispy import Runtime
inbuilt_num = 7.0
factorial = lambda n: 1.0 if n <= 1.0 else n * factorial(n-1)
rt = Runtime(maxLoopTime = 13, maxDepth = 100)
rt.addNatives({'factorial' : factorial, 'inbuilt_num' : 7.0})
demoProgo = 'writeln(factorial(inbuilt_num));'
rt.run(demoProgo) # prints 5040
```

Please note that the LittleJ program may redefine all natives. And a native variable may be initialized (as a fresh variable) in any non-global environment.

More will soon be added. As already noted, THIS IS WORK IN PROGRESS.
There are more caveats, but error messages should be helpful enough.
