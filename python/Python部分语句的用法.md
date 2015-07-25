# global语句

```python

global_stmt ::=  "global" identifier ("," identifier)*

```

Python中的`global`语句的作用是在当前作用域中声明一个全局变量。在`global`语句后面的标识符被解释器认为是一个全局变量（在Python中，由于名字绑定的规则，导致如果在局部作用域中对一个全局变量进行赋值操作，则在赋值的时候会发生名字绑定操作，也就是说会重新绑定一个和全局变量同名的局部变量，这就导致全局名字被屏蔽，所以为了在局部作用域中使用全局变量，需要使用global声明）。

global语句具有以下2点限制：

* 在通过global声明的标识符，在当前作用域中不能出现在global语句之前。
* 通过global语句声明的标识符，不能是函数的参数，也不能是for循环的控制变量（for语句块并不创建一个作用域，所以和for语句块外面的作用域一致）、类定义的类名，函数名以及import语句中的名字。

CPython实现细节：

当前CPython的实现对上面的限制并没有强制要求，但是程序应该遵循这些限制，可能在未来版本中会加入这些限制。

注意：

global语句是Python解释器的一个命令，global命令会在代码解析阶段起作用。特别的，global语句出现在用于exec()内建函数的字符串或者代码对象中的时候，并不会影响调用这个exec()内建函数的函数作用域，而且在当前作用域中的global声明并不会影响exe()内建函数中的字符串或者代码对象的作用域。（这两条规则同样适用于eval()和compile()函数）。


## 例子

```python

>>> a = 10
>>> def function():
	global a
	a += 1
	print(a)

	
>>> function()
11
>>> a
11

```

# nonlocal语句

```python

nonlocal_stmt ::=  "nonlocal" identifier ("," identifier)*

```

nonlocal语句使得出现在语句后面的标识符引用在外围作用域中绑定的名字。这个关键字的作用很重要，因为Python中名字绑定的规则的限制（Python优先搜索当前作用域中的名字，如果出现赋值操作，则会进行名字绑定操作），使得如果不使用nonlocal，则对于不可变对象是只读的。nonlocal语句使得可以绑定名字到外围作用域中的名字，这使得可以不会受到Python名字绑定规则的限制读写自由变量。

在nonlocal语句后面的标识符，必须引用出现在外围作用域中的名字。
在nonlocal语句后面的标识符，不能和已经出现在局部作用域中的名字发生冲突。

## 例子

```python

>>> def outer():
	a = 10
	def inner():
		a += 1
		print(a)
	return inner

>>> f = outer()
>>> f()
Traceback (most recent call last):
  File "<pyshell#63>", line 1, in <module>
    f()
  File "<pyshell#61>", line 4, in inner
    a += 1
UnboundLocalError: local variable 'a' referenced before assignment

# use nonlocal
>>> def outer():
	a = 10
	def inner():
		nonlocal a
		a += 1
		print(a)
	return inner

>>> f = outer()
>>> f()
11
>>> f()
12
>>> f()
13

```