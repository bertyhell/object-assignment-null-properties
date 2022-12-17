# Proposal for object property nullish coalescing operator

This proposal shows a new syntax for assigning properties to an object literal but only if the object is not falsy/null or undefined.

Short example:
```javascript
const myPersonObj = {
	firstName: person.name,
	work?: person.occupation,      // Only set work if person.occupation is truthy, by using the "?:" operator
	street??: person.address[0]    // Only set street is person.address[0] is not null and not undefined, by using the "??:" operator
};
```

## Status

Champion: Bert Verhelst, Stage: -1

## Motivation

This code paradigm often returns in javascript code where you want to assign a property to an object if the value is defined (truthy). If it is not, then the property should not be put on the object.

Example of the current way of writing this:
```javascript
const person = {
  age: 50,
  name: 'John',
  occupation: undefined
}

const myPersonObj = {
  firstName: person.name,
  ...(person.occupation ? {work:person.occupation} : {}) 
};

// result
// { firstName: 'John' }
```

Or also:
```javascript
const person = {
  age: 50,
  name: 'John',
  occupation: undefined
}

const myPersonObj = {
  firstName: person.name,
};

if (person.occupation) {
  myPersonObj.work = person.occupation;
}

// result
// { firstName: 'John' }
```

## Problem statement

Both these ways of writing the code are quite verbose. And the first way is somewhat less verbose, but at a cost of clarity. Because it contains so many parentheses.

## Proposed solution

The proposed solution defines a new literal object assignment: "?:" Which would only assign the work property if the value on the right: person.occupation is truety. eg: it is not an empty string, not null, not undefined and not zero.

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: undefined
}

const myPersonObj = {
	firstName: person.name,
	work?: person.occupation
};

// result
// { firstName: 'John' }
```

We can also introduce the [nullish coalescing operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing) as a secondary assignment operator: "??:". This would only assign the property "work" if person.occupation is not `null` and not `undefined`.

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: undefined
}

const myPersonObj = {
	firstName: person.name,
	work??: person.occupation
};

// result
// { firstName: 'John' }
```


## Other examples and edgecases

Empty string is also considered falsy, so will not be set when the "?:" operator is used.

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: ''
}

const myPersonObj = {
	firstName: person.name,
	work?: person.occupation
};

// result
// { firstName: 'John' }
```

Empty string is considered a value for the nullish coalescing operator, so will be set if the "??:" operator is used

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: ''
}

const myPersonObj = {
	firstName: person.name,
	work??: person.occupation
};

// result
// { firstName: 'John', work: '' }
```

The numeric value zero is also considered falsy, so will not be set when the "?:" operator is used.

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: 0
}

const myPersonObj = {
	firstName: person.name,
	work?: person.occupation
};

// result
// { firstName: 'John' }
```

The numeric value zero is considered a value for the nullish coalescing operator, so will be set if the "??:" operator is used

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: 0
}

const myPersonObj = {
	firstName: person.name,
	work??: person.occupation
};

// result
// { firstName: 'John', work: 0 }
```

Works with index brackets to set the property

```javascript
const person = {
	age: 50,
	name: 'John',
	occupation: null
}

const myPersonObj = {
	firstName: person.name,
	['work']??: person.occupation
};

// result
// { firstName: 'John' }
```

## Other considerations

I was also thinking about how this would work with shorthand notation for object literals. But i don't see a clean way of getting this to work.

eg:
```javascript
const occupation = null;

const myPersonObj = {
	firstName: person.name,
	occupation?: occupation
};

// result
// { firstName: 'John' }
```

This could be written in shorthand like this:
```javascript
const occupation = null;

const myPersonObj = {
	firstName: person.name,
	?occupation
};

// result
// { firstName: 'John' }
```

But i'm not entirely sure about the syntax being clear enough and `occupation?: occupation` is quite short already, so i'm not including it in the formal proposal. But feedback is welcomed.

## Looking for feedback

- Should we add both operators? "?:" and "??:", or should we just add "?:" and have the behavior follow the modern nullish coalescing operator (only on null or undefined).
- What impact does this have on the interpreter process? (V8). Is this an easy change, or would detecting these operators cause the interpretation of object literals to significantly slow down?
- Feedback on shorthand notation. See the chapter "Other considerations"
