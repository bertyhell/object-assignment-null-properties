# Proposal for object property nullish coalescing operator

This proposal shows a new syntax for assigning properties to an object literal but only if the value is not undefined/null.

Short example:
```javascript
const myPersonObj = {
	firstName: person.name,
	work??: person.occupation,      // Only set work if person.occupation is not null and not undefined, by using the "??:" operator
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

The proposed solution defines a new literal object assignment: "??:" Which would only assign the work property if the value on the right: person.occupation is not null/undefined. The behvavior would follow the existing [nullish coalescing operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing).

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

### I was considering another operator: "?:" which would check for falsey values. eg: empty string, zero, null and undefined. But with the "??:" operator there is already a susinct way of writing that: (credit to claudepache in [comment](https://es.discourse.group/t/object-nullish-coalescing-operator/1564/2))

```javascript
const occupation = '';

const myPersonObj = {
	firstName: person.name,
	occupation??: occupation || null
};

// result
// { firstName: 'John' }
```

### I was also thinking about how this would work with shorthand notation for object literals. But i don't see a clean way of getting this to work.

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
