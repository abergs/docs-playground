# JavaScript Client Reference

The passwordless.dev JavaScript client is used by your frontend to make requests to end-users browsers' WebAuthn API and requests to the passwordless.dev APIs.

## Installation

To install the passwordless.dev JavaScript client:

<CodeSwitcher :languages="{bash1:'yarn',bash2:'npm',es6:'ES6',http:'http'}">
<template v-slot:bash1>

```bash
yarn add @passwordlessdev/passwordless-client
```
For all cases, your frontend must import the library to call the methods in this document:
```js
import { Client } from '@passwordlessdev/passwordless-client';
```
</template>
<template v-slot:bash2>

```bash
npm install @passwordlessdev/passwordless-client
```
For all cases, your frontend must import the library to call the methods in this document:
```js
import { Client } from '@passwordlessdev/passwordless-client';
```
</template>
<template v-slot:es6>

```http
<script src="https://cdn.passwordless.dev/dist/0.3.0/passwordless.min.mjs" crossorigin="anonymous"></script>
```
For all cases, your frontend must import the library to call the methods in this document:
```js
import { Client } from "https://cdn.passwordless.dev/dist/0.3.0/passwordless.min.mjs"
```
</template>
<template v-slot:http>

```http
<script src="https://cdn.passwordless.dev/dist/0.4.0/passwordless.iife.js" crossorigin="anonymous"></script>
```
For all cases, your frontend must import the library to call the methods in this document:
```http
<script>
var p = new Passwordless.Client({});
</script>
```

</template>
</CodeSwitcher>

## .register()

Call the `.register()` method to fetch a token from your backend to register with the end-user's device, for example:

```js

const p = new Passwordless.Client({ apiKey: "demo:public:6b08891222194fd1992465f8668f" });
const myToken = "...";
try {
    await p.register(myToken);
  } catch (e) {
}
```

## .signin()

Call `.signin()` methods to trigger token verification on your backend. There are a few different `.signin()` methods available:

|Method|Description|Example|
|------|-----------|-------|
|`.signinWithAutofill`||`verify_token = await p.signinWithAutofill();`|
|`.signinWithAlias`||`verify_token = await p.signinWithAlias(email);`|
|`.signInWithId`||`verify_token = await p.signInWithId(userId);`|

```js
const p = new Passwordless.Client({ apiKey: "demo:public:6b08891222194fd1992465f8668f" });
try {
    let verify_token = null;
    // alternative 1:
    // use autofill to sign in. This enables browsers to suggest passkeys for any input that has autofill="webauthn",
    // e.g. <input type="text" placeholder="Your email" autofill="username email webauthn" />
    verify_token = await p.signinWithAutofill();
    // alternative 2:
    // Not specyfing an alias will allow the user to select any available credentials on the device or insert a security key
    verify_token = await p.signinWithAlias(null);
    // alternative 3:
    // Use an alias as inputed by the user to authenticate the user
    const email = ""; // get email from input field
    verify_token = await p.signinWithAlias(email);
    // alternative 4:
    // If you know the userid beforehand (e.g. they are already logged in but you want to re-auth them)
    // You can use signInWithId to do a re-auth
    const userId = ""; // fetch userid from database or localstorage
    verify_token = await p.signInWithId(userId);
    // success!
    // todo: call your backend to verify the verify_token
} catch (e) {
    // error    
}
```

## .isBrowserSupported()

Call the `.isBrowserSupported()` static method to check if the end-user's browser supports FIDO2 WebAuth passkey-based authentication, for example:

```js

if (Passwordless.isBrowserSupported() === true) {

}
```

## .isPlatformSupported()


Call the `.isPlatformSupported()` static async method to check if the end-users device supports platform authentication, for example:

```js
if (await Passwordless.isPlatformSupported() === true) {

}
```

## .isAutofillSupported()

Call the `.isAutofillSupported()` static async method to check if the end-user's device supports ________, for example:

```js

if (await Passwordless.isAutofillSupported() === true) {

}
```
