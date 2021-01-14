<!-- 
.. title: Debugging C/C++ libraries called by Python
.. slug: debugging-cc++-libraries-called-by-python
.. date: 2015-12-16 14:32:06 UTC-06:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

I've been debugging a C++ library that I wrote and am calling from Python via a [CFFI](https://cffi.readthedocs.org/en/latest/) interface.  After an hour or two of debugging with print-statements without resolution, I decided to figure out how to use a proper debugger. I'm very comfortable on the command-line and have used `gdb` in this way often in the past, so am happy I found an easy solution.  The instructions that follow should work for any C/C++ library built with debug flags enabled (i.e. the `-g` compiler flag) that is called from Python.  While I am using CFFI, it should also work for [ctypes](https://docs.python.org/2/library/ctypes.html) or [SWIG](http://www.swig.org/) interfaces.  Also the commands below are specific to the `lldb` debugger, but could be easily translated to `gdb`.

First, in two separate terminal windows, launch `ipython` and `lldb`.  At the IPython prompt, type:

````
In [1]: !ps aux | grep -i ipython
````

and look for the process ID (pid) of the IPython session.  Let's assume the process ID in our case is `1234`.  Go to the `lldb` command prompt and attach to the IPython session with

````
(lldb) attach --pid 1234
````

followed by

````
(lldb) continue
````

to release the process.  Now your ready to set breakpoints, etc.  For example, to set a breakpoint on line 400 of a `myfile.cpp` we would type

````
(lldb) breakpoint set -f myfile.cpp -l 400
````

Assuming `myfile.cpp` is compiled into a library that is called by the Python script/module `myscript.py`, we can now run the Python code in the IPython terminal

````
In [2]: run myscript.py
````

Back in the `lldb` terminal we should see our code stopped on line 400.  Debug away...

P.S.  I found the bug after 5 minutes in a proper debugger...
