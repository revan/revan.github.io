---
layout: post
title: "Python list comprehensions in MATLAB"
---

My classes this semester are requiring a whole lot MATLAB, with its ...unusual syntax and focus on matrices over loops.

I was pretty excited to find out [MATLAB has some basic support for python calling](http://www.mathworks.com/help/matlab/getting-started_buik_wp-3.html).

I got right to work on getting list comprehensions, a feature I'm dearly missing.


```MATLAB
function array = pylist(statement, vars)
    workspace = py.dict;
    for v = 1:length(vars)
        update(workspace, py.dict(pyargs(cell2mat(vars(v)), evalin('caller', cell2mat(vars(v))))))
    end
    
    cell2mat(cell(py.eval(statement, workspace)))
end
```

## Usage

The function takes a string python expression and a cell array of variable names to pass in, and returns an array of the results.

```MATLAB
>> x = 6

x =

	6

>> pylist('[q**2 for q in range(int(x))]', {'x'})

ans =

	0	1	4	9	16	25
```

There's some cool bits going on here, especially with the variable loading.
MATLAB exposes the current workspace through `evalin()`, letting us fetch variables by name.
`x` needs int casting because it's comes out as a float.
Most of the code is in processing the python data type wrappers. 

Of course, the minute I finished this I realized MATLAB has its own alternative to list comprehensions, with the function [arrayfun()](http://www.mathworks.com/help/matlab/ref/arrayfun.html).

Oops.

