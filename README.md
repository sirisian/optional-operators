# Optional Operators

## Introduction

( Based on tangents in this discussion: https://es.discourse.group/t/optional-chaining-assignment/1149/14 )

Optional chaining removed a lot of verbose ```if``` statements; however, it did not apply to left-hand side checks in assignment operators which are very similar. So while this works:

```js
a?.b?.c?.d();
```

A similar property access and assignment requires ```if``` checks:

```js
if (a?.b?.c?.d) {
	a.b.c.d = 1;
}
```

This proposal would allow left-hand side optional chaining and add a new prefix to all assignment operators like: ```?<assignment operator>```. These operators would only evaluate the right hand side if the left-hand side is not undefined or null.

```js
a?.b?.c?.d = 1;
```

Note that if ```d``` must also not equal undefined or null then one would write:

```js
//if (a?.b?.c?.d ?? false) {
//	a.b.c.d = 1;
//}
a?.b?.c?.d ?= 1;
```

If most of the path exists then only the optional assignment operators need to be used.

```js
const a = { b: { c: {} } };
a.b.c.d ?+= 1;
a; // { b: { c: {} } }
```

The ```??=``` operator would not have an optional variation as it does not make sense.

## Differences in Execution

The assignment operator ```?=``` would behave differently than the ```=``` operator as it must evaluate the left-hand side.

```js
const o = {
	x: 1,
	get a() {
		console.log('get');
		return this.x;
	},
	set a(value) {
		console.log('set');
		this.x = value;	
	}
};
o.a = 5; // 'set'
o.a ?= 5; // 'get' 'set'
```

Other operators like ```+=``` already access the left-hand side so their behavior is unchanged.

## Optional Increment and Decrement

For completeness the increment and decrement can also be optionally executed:

```js
let a = null;
++?a;
a?++;
a?--;
--?a;
```

## Questions

Why not just implicitly check the whole left hand side rather than requiring explicit optional chaining?

```js
a.b.c.d ?= 1;
```

Having that expand into ```if (a?.b?.c?.d ?? false)``` would be convenient, but it's also hiding a lot of branching. By forcing the user to be explicit it lowers that penalty. It also doesn't allow for ```d``` to not be set when it's ```undefined``` or ```null```. Sometimes you don't want to add a property to an object if it doesn't already exist. (Though that might be niche). A better example is like:

```js
//a.b.c.d = null;
//a.b.c.d = 20;
a.b.c.d ?+= 10; // Keep d null else add 10 
```
