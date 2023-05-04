# Errors

Welcome to the fun page! Here's a collection of the difference errors you might run into.

## Problem Details

All our errors are formatted to the `Problem Details` [RFC-7807](https://www.rfc-editor.org/rfc/rfc7807).

That means our HTTP API and Client Side code return errors that look like this:

```json5
{
  "type": "https://docs.passwordless.dev/errors#missing_register_token",
  "title": "The token you sent was not correct. The token used for this endpoint should start with 'register_'. Make sure you are not sending the wrong value.",
  "status": 400,
  "errorCode": "missing_register_token"
}
```

### How do I see errors on the client side?

Please take a look at our front end examples. Our Javascript functions return a object containing either a token or a error `{ token: string, error: ProblemDetails}`.

```ts
const { token, error } = p.signinWithDiscoverable();
if(error) {
  console.error(error); // console logs a Problem Details object
}

```

## List of Error codes

### 400 missing_register_token

You receive this error when you call `p.register(registerToken)` - but the value of `registerToken` is not valid.

### How to resolve?

Make sure you have a expected value in your `registerToken`. Hint: It should start with `register_`.

You obtain a register token from your backend by calling the `/register/token` endpoint.

### 400 unknown_credentials
