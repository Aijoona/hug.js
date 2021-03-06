hug.js
======

(Useless) functional wrappers for native objects/values


```javascript	
var Color = hug()

Color('set')('init', function($self, r, g, b) {
	$self
		('set')('r', r || 0)
		('set')('g', g || 0)
		('set')('b', b || 0)
})

Color('set')('+', function($self, another) {
	return Color('new')(
		$self('r') + another('r'),
		$self('g') + another('g'),
		$self('b') + another('b')
	)
})

// ...
```


Features
--------

- Private properties
- Virtual properties
- Object binding
- A lot of parens!

Standard methods of the instances:
-------------------------------------

- value([newValue])
- id()
- set(name, value, modifiers)
- get(name)
- new([args])
- bind(target)
- unbind()
- has?(prop)
- is?(type)
- missing(name, args)
- toString()

##A few (useless) examples##


###Wrapping native values###

```javascript
var one = hug(1)

one // "hug@41"
one() // 1
one('toString') // function
one('get')('toString') // function
one('toString')() // "hug@41"
one('get')('toString')() // "hug@41"
```

###Setting properties###

```javascript
var one = hug(1)

one('set')('myProp', 'value!')
one('myProp') // "value!"

// Chained definitions
one
	('set')('a', 1)
	('set')('b', 2)
	('set')('c', 3)
	
// Batch syntax
one('set')({
	d: 4,
	e: 5	
})	
```

###Checking for properties###

```javascript
var subject = hug()

subject('set')('myProp', 'value!')
subject('has?')('myProp') // true

subject('has?')('anotherProp') // false
subject('anotherProp') // Error!
subject('get')('anotherProp') // null
```

###Setting 'methods' (callable properties)###

```javascript	
var one = hug(1)

// $self is a privileged self-reference (can get/set private properties)
one('set')('+', function($self, another) {
	return $self() + another
})

one('+')(2) // 3

one // "hug@41"

one('set')('toString', function($self) {
	return String($self())
})

one; // "1"
```

###Using prefix syntax###

```javascript	
var one = hug(1)

one('set')('+', function($self, a, b, c) {
	return $self() + a + b + c
})

one('+', 2, 3, 4) // 10

// similar to
one('+')(2, 3, 4) // 10
```

###Catching virtual properties###

```javascript	
var myObj = hug()

myObj('set')('missing', function($self, name) {
	return 'Requested: ' + name
})

myObj('a') // "Requested: a"
myObj('foo') // "Requested: foo"
```

###Spawning instances###

```javascript	
// Create a wrapped object
var Color = hug()

// Set the constructor
Color('set')('init', function($self, r, g, b) {
	$self
		('set')('r', r || 0)
		('set')('g', g || 0)
		('set')('b', b || 0)
})

// A few methods
Color('set')('+', function($self, another) {
	return Color('new')(
		$self('r') + another('r'),
		$self('g') + another('g'),
		$self('b') + another('b')
	)
})

Color('set')('toString', function($self) {
	return '#' +
			$self('r').toString(16) + 
			$self('g').toString(16) + 
			$self('b').toString(16)
})

var red = Color('new')(255, 0, 0)
var blue = Color('new')(0, 0, 255)

red.toString() // #ff0000
blue.toString() // #0000ff

var violet = red('+')(blue)

violet('r') // 255
violet('g') // 0
violet('b') // 255

violet('is?')(Color) // true
violet.toString() // #ff00ff
```


###Lazy binding###

```javascript
Color('set')('brightness', function($self, value) {
	return $self
		('set')('r', $self('r') * value)
		('set')('g', $self('g') * value)
		('set')('b', $self('b') * value)
})

var main = Color('new')(140, 30, 90)

// 'darker' is a binded reference 'main'
var darker = hug()('bind')(function() {
	return main('new')()
		('brightness')(0.7)
})

darker('r') // 98 <- (main.r * 0.7)

main('set')('r', 70)

darker('r') // 49 <- (main.r * 0.7)
```


hug.js is licensed under the terms of the MIT License.