# Frontend Frameworks

You can use passwordless.dev with any frontend framework by implementing calls to the passwordless.dev API, but this document will provide some code examples, guidelines, and other help integrating passwordless.dev with popular frameworks.

## Vue 3 <Badge text="example" type="warning"/>

Coming Soon.

## React 17 <Badge text="example" type="warning"/> <Badge text="demo" type="tip"/>

Coming Soon.

## Errors

while working with frontend code, several errors may be encountered. All Passwordless.dev errors are formatted to the `Problem Details` [RFC-7807](https://www.rfc-editor.org/rfc/rfc7807).

### How do I see errors on the client side?

Our Javascript functions return a object containing either a token or an error `{ token: string, error: ProblemDetails}`.

```ts
const { token, error } = p.signinWithDiscoverable();
if(error) {
  console.error(error); // console logs a Problem Details object
}

```
