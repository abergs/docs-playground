# Get Started

Passwordless.dev is a software toolkit that helps web developers to support FIDO2 WebAuthn passkeys for authentication by their end-users. In this guide, we'll chart the quickest path to implementing passwordless.dev for your website.

::: tip
This guide will skip over some conceptual basics in order to get you started as quickly as possible. Check out [Concepts & Mechanics](concepts) for in-depth discussion of the ideas used by passwordless.dev.
:::

In this guide we'll provide JavaScript examples, however you can check out sample code, guidelines, and tips for other languages in [Backend Languages](backend) and [Frontend Frameworks](frontend).

## Sign up

Sign up for a passwordless.dev account [here](https://adminconsole-devtest.azurewebsites.net/Account/Login). Bitwarden offers a free passwordless.dev account, or paid plans that [unlock certain tiers of usage and features](plans).

When you sign up you'll land on the [Admin Console](admin-console), your primary GUI for creating and configuring applications, monitoring application usage, and managing billing:

<img :src="$withBase('/admin-console.png')" alt="Admin Console">

## Create an application

Select the **Create Application** button and give your new application an **Application name** and **Description**. For each application, a set of [API keys](concepts) will be generated. You'll use these API keys for authentication with the passwordless.dev API. Save your public key and private secret somewhere safe, like [Bitwarden Secrets Manager](https://bitwarden.com/help/secrets-manager-overview).

## Install the library

Next, install the [passwordless.dev JavaScript client library](js-client), either globally or as an ES6 module within your application. This library will allow your application to interact with the passwordless.dev API and with browsers' WebAuthn API. To install the library:

```npm
import { Client } from '@passwordless/dev/passwordless-client';
```

## Build a registration flow

Next, implement a workflow on your backend and frontend for registering a passkey. Code that you write to do this must:

<Badge text="backend" type="warning"/>
1. Call the passwordless.dev API's `/register/token` endpoint ([learn more](api/#register-token)) with, at a minimum, a `userId`, `username`, and `displayname` for the user, for example:

```js
const payload = {
  "userId": "107fb578-9559-4540-a0e2-f82ad78852f7",
  "displayname": "Anders Åberg",
  "username": "anders@passwordless.dev",
  "attType": "None",
  "authType": "platform",
  "userVerification": "preferred",
  "expiresAt": "2021-08-01T14:43:03Z"
};

var token = await fetch(apiurl + "/register", {
    method: "POST",
    body: JSON.stringify(payload),
    headers: { "ApiSecret": "myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4", "Content-Type": "application/json"}
});
```

Successful implementation will return a registration token to the client, for example:

```json
{
  "token": "wWdDh02ItIvnCKT_02ItIvn..."
}
```

<Badge text="frontend" type="tip"/>
2. Initiate, client-side, the WebAuthn process to create and store a WebAuthn credential using the returned token ([learn more](js-client)), for example:

```js
var p = new Passwordless.Client({
    apiKey: "demo:public:6b08891222194fd1992465f8668f"
});

var myToken = await fetch("/example-backend/passwordless/token").then(r => r.text());

try {
    await p.register(myToken);
} catch (e) {
}
```

## Build a signin flow

Next, implement a workflow on your backend and frontend for signing in with a passkey. Code that you write must:

<Badge text="frontend" type="tip"/>
1. Call the passwordless.dev API's `/signin` endpoint ([learn more](js-client)) with the user's `userId` or alias to initiate the backend operation to verify the user's token, for example:

```js
var p = new Passwordless.Client({
    apiKey: "demo:public:6b08891222194fd1992465f8668f"
});

var alias = "anders@user.com";

var token = await p.signinWithAlias(alias);

var verifiedUser = await fetch("/example-backend/passwordless/signin?token=" + token).then(r => r.json());
if(verifiedUser.success === true) {
}
```

If the sign-in is cryptographically successful, a token is returned to the client, which then forwards the token to your backend.

<Badge text="backend" type="warning"/>
2. Call the passwordless.dev API's `/signing/verify` endpoint ([learn more](api/#signin-verify)) with the user's token, for example:

```js
const token = { token: req.query.token };

const response = await fetch(apiurl + "/signin/verify", {
    method: "POST",
    body: JSON.stringify(token),
    headers: { ApiSecret: API_SECRET, 'Content-Type': 'application/json' }
});

var body = await response.json();
if (body.success) {
    console.log("Succesfully verfied signin for user", body);
} else {
    console.warn("Sign in failed", body);
}
```

Successful implementation will return a success response including the user's `userId`, for example:

```json
{
  "success": true,
  "userId": "123",
  "timestamp": "2021-08-01T01:33:36.9773187Z",
  "rpid": "example.com",
  "origin": "http://example.com:3000",
  "device": "Chrome, Windows 10",
  "country": "",
  "nickname": "Home laptop",
  "credentialId": "Mq1ZhrHBmhly34YaO/uuXuNuf/VCHDkuknENz/LZJR4=",
  "expiresAt": "2021-08-01T01:35:36.9773193Z"
}
```

## Next steps

Congratulations on mastering a basic implementation of passwordless.dev! Next:

- Check out other [Backend Languages](backend) and [Frontend Frameworks](frontend) to find the best fit for your application.
- Dig into the functionality offered by the [Admin Console](admin-console).
- Figure out the [best plan](plans) to fit the needs of your application or business.
