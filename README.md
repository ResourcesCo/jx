# jx: JSX-inspired template language for JSON

## Quick Intro

``` ts
import jx from '@resources/jx'

const input = {
  "message": [{$concat: ["Hello, ", {$ref: 'name'}, "!"]}],
  "name": "World"
}

console.log(JSON.stringify(jx(input)))
// {"message": "Hello, World", "name": "World"}
```

`jx` will go through the input depth-first, and take any object with a single
key that starts with a dollar sign, and look for a function, and if it
finds one that exists, replace it with the result of the function identified
by the key applied to the value. Ordinary functions like `$concat` only have
access to the value inside the jx `{"$key": "value"}` object, but
context-aware functions can be added explicitly when creating the `jx`
evaluator. `$ref` is such a function. It can ask for a value inside the
document. `jx` functions don't have direct access to other `jx` functions
unless they're, so ordinary functions won't unexpectedly get access to other
parts of the document. The `@resources/jx` library has all the available
functions defined in one place, so it's easy to see which function has access
to what (most only have access to the enclosed values). The
`@resources/jx-core` can be used to create a jx evalutator from scratch, that
does a no-op if no functions are defined.

If a function isn't found, it will leave the data in place. The `$` notation
is borrowed from MongoDB, and MongoDB keys such as `$or` are avoided. In `jx`,
the equivalent is `{"$expr": {"or": [{"$ref": "cold"}, {"$ref": "rainy"}]}`.
Data can be marked as quoted with `$value`, and can more completely be quoted
with `$quote` (with the right function definitions, almost anything can be
overridden, though!).

## FAQ

### How does this compare to JSX?

It's very similar. As [Ryan Florence](http://ryanflorence.com/)
[said](https://medium.com/@ryanflorence/jsx-is-just-a-different-way-to-call-a-function-you-cant-return-two-functions-calls-87c4bc081664),

> JSX is just a different way to call a function.

So is jx.

However, while JSX is transformed by Babel or TypeScript into JavaScript, and
then evaluated with JavaScript, jx is evaluated without using eval or a
compiler. This makes it more practical for untrusted input.

### How will this interoperate with JSX?

The `@resources/jx-jsx` provides a way to build React/JSX elements from jx.
The tags beginning with capital letters (Components) need to be passed in
when setting up the jx evaluator. JSX is namespaced, so `<div>` is
referenced by `$jsx.div`. An expression can also be wrapped with `$jsx` so
`$div` calls into the JSX factory and calls to jx functions are prefixed
with `$jx.`. Inside that, an expression be wrapped with `$jx` which
restores normal behavior inside that function.

JavaScript evaluation can be enabled and used by wrapping expressions that
evaluate to strings with `$js`. So,
`<button onClick={() => alert('Hello!')}>Hello</button>` can be written as
`{"$jsx.button": {"$js": "() => alert('Hello!')"}}`. This can be evaluated
with the JavaScript engine or a static evaluator.

In addition to that, the result of a jx function call can be passed in.
Since these are just functions, they can return a function, and it can act
as an event handler.

### How does this compare to Lisp?

LISP is an acronym for "list processing". This gives no preference to
whether a function takes an array, an object, or any other type as its one
argument. As such, it can appear more like data and less like code than
Lisp.

### Can I use this without so many dollar signs?

Yes. A function can take .

## `@resources/jx`

## `@resources/jx-core`

## `@resources/jx-ref`

## `@resources/jx-config`

This provides for building a config store and a secret store. The secret
store is aware of where within the document it's getting inserted, and can
access the rest of the document, and each secret can have restrictions on
it based on that. For instance, in a document that describes an HTTP
request, the secret should only be allowed in the `Authorization` header,
and only when the URL is . Secrets are added last, so . The config store settings are added first,
like `$ref` objects.

## `@resources/jx-jsx`

# Acknowledgements

The build scripts are based on those from [Stimulus](https://stimulusjs.org/),
which like jx, uses [TypeScript](https://github.com/Microsoft/TypeScript)
and [Lerna](https://lernajs.io/).