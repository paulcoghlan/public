# Javascript

## Typescript

Logging output:

```js
  console.info("some log line: " + JSON.stringify(anObject));
```

## React

## Intro
- **Component** is a Javascript function that returns HTML, starts with capital letter (vs. HTML)
- Markup uses JSX (stricter than HTML)
- Components can’t return multiple JSX tags, need to wrap in single parent, e.g: `<div>...</div>` or an empty `<>...</>` 
- Respond to events using `handleXXXX()` functions
- Update with `useState()`
	- const [something, setSomething] = useState(0);
	- something is current state
	- setSomething() can update state
