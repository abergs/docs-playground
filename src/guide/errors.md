# Errors

While errors are never fun, we've tried our best to make it easy to determine why you're receiving an error. Here's a collection of the different errors you might run into and how to solve them.

## Problem Details

All errors are formatted as `Problem Details` [RFC-7807](https://www.rfc-editor.org/rfc/rfc7807), meaning our HTTP API and Client Side code return errors that look like this:

```json5
{
  "type": "https://docs.passwordless.dev/errors#missing_register_token",
  "title": "The token you sent was not correct. The token used for this endpoint should start with 'register_'. Make sure you are not sending the wrong value.",
  "status": 400,
  "errorCode": "missing_register_token"
}
```

### How do I see errors on the client side?

Please take a look at our front end examples. Passwordless.dev JavaScript functions return a object containing either a token or an error: `{ token: string, error: ProblemDetails}`.

```ts
const { token, error } = p.signinWithDiscoverable();
if(error) {
  console.error(error); // console logs a Problem Details object
}

```

## List of Error codes

### missing_register_token

##### Reason
You receive this error when you call `p.register(registerToken)` but the value of `registerToken` is empty or invalid.

##### Solution

Make sure you have an expected value in your `registerToken`. Obtain a register token from your backend by calling the `/register/token` endpoint. It should start with `register_`.

### unknown_credential

##### Reason
You receive this error when you call `p.signinWith*()` but the passkey that was used is not registered in our system. This can happen if the passkey has been deleted on the server (e.g. removing it in your app UI or the Admin Console) but still exists on a users device.

##### Solution

The solution is to remove the passkey from the user device. This can be done in the Browser's settings or on the Operating Systems Credential Manager.

### missing_userid

You receive this error while calling `/register/token` and fail to supply the UserId property in the json payload. 

##### Reason
When creating a reigster_token you must supply a valid UserId.

##### Solution

Add a UserId to your payload, e.g:

```json5
{
  "UserId": "123",
  "Username": "pjfry@passwordless.dev"
}
```

### old_token

You receive this error when you're trying to use a token to either A) register a passkey or B) verify a signin.  

##### Reason
The token you're trying to use have expired. By default, tokens are only valid for 120 seconds.

##### Solution

Make sure you use your tokens immediately when they are being created. Tokens are meant to be short lived. If your use case requires it, the expiration can be configured when creating the token.


### alias_conflict

You receive this error if you're trying to set or update the aliases of a user.

##### Reason
The alias you are trying to use is already being used by a different UserId.

##### Solution

You need to use a unique alias per userId. You can remove the alias from an existing user by calling the `/alias` endpoint.