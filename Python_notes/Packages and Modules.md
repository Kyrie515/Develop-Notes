## Packages and Modules

#### 1.modules in Python

Modules refer to a file containing Python statement and definitions. For example, if a file contains Python code and is named `example.py`, then it is considered as a module and the name of this module is `example`,

Modules can be imported but packages cannot, we must keep that in mind: The file following `import`statement can only be functions, classes or modules.



#### 2.Python module search path

While importing a module, Python will look at several places. Interpreter first looks for a built-in module. If not found, it then looks at a list of directories defined in `sys.path`. The search is in this order:

- The current directory
- `PYTHONPATH`.So when a module is not found we can try `export PYTHONPATH`command in linux
- The installation-dependent default directory

```shell
>>> import sys
>>> sys.path
>>> 
['', '/Library/Frameworks/Python.framework/Versions/3.8/lib/python38.zip', '/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8', '/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/lib-dynload', '/Library/Frameworks/Python.framework/Versions/3.8/lib/python3.8/site-packages']
```



#### 3.Python packages

Python has packages to keep analogous files or directories, which is good for hierarchy of our projects.

A Python package can have sub-packages and modules. In order to make Python consider a directory as a package, the directory must have a file named `__init__.py`



**created by Kyrie**
