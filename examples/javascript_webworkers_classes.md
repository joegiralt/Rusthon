WebWorker Transferable Class Instances
--------------------------------------

Functions and methods inside the webworker can have their arguments and return types declared using Python3 style annotation type syntax.
The `SharedClass` prototype is restored each time it is send to/from the webworker.
This allows you to send objects that contain data and keep methods on the the top level object.
Note: sub-objects of the transported do not have their class types automatically restored.

See Also
--------
* [javascript_timeout_loops.md](javascript_timeout_loops.md)
* [javascript_timeout_webworkers.md](javascript_timeout_webworkers.md)


@myapp
```rusthon
#backend:javascript
from runtime import *

def show(txt):
	document.getElementById('CONTAINER').appendChild(
		document.createTextNode(txt + '\n')
	)

class SharedClass:
	def __init__(self, x,y,z):
		self.x = x
		self.y = y
		self.z = z

	def foobar(self):
		return self.x + self.y + self.z


with webworker:
	def somefunction() -> SharedClass:
		s = SharedClass(10,20,30)
		return s

	class MyWorker:
		def send(self, obj:SharedClass ) -> SharedClass:
			print obj
			print obj.foobar()
			obj.x = 10
			return obj

		def somemethod(self) -> SharedClass:
			s = SharedClass(100,200,300)
			return s



def main():
	global WORKER
	show('spawn worker...')
	WORKER = spawn( MyWorker() )

	show('creating SharedClass')
	a = SharedClass(1,2,3)
	print(a)
	show(a.foobar())

	show('sending data to worker')
	WORKER <- a

	show('getting data from worker')
	b = <- WORKER
	show(b.foobar())

	c = <- somefunction()
	show(c.foobar())

	d = <- WORKER.somemethod()
	show(d.foobar())

```

@index.html
```html
<html>
<head>
</head>
<body onload="main()">
<pre id="CONTAINER">
</pre>
<@myapp>
</body>
</html>
```