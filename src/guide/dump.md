# dump

## From Getting Started:

::: tip
Want to see examples in other types of applications? Refer to our [Backend Languages](backend) and [Frontend Frameworks](frontend) articles.
:::


## Introduction



## Terminology and concepts

This section will only describe concepts specific to the Passwordless API. See [What is Webauthn and FIDO2?](what-is-webauthn-and-fido2) for more terminology.

When using the Passwordless API, you will encounter three type of tokens:

* **ApiKey** `example:public:6b086b1e...` This is a Public API key, safe and intended to be included client side. It allows the browser to connect to our backend and initiate key negotiations and assertions.
* **ApiSecret** `example:secret:4fd1992...` This is a Secret API key and should be well protected. It allows your backend to verify sign ins and register keys on behalf of your users. ([Create an account](https://www.passwordless.dev/create-tenant/) to get your API keys.)
* **Token** `wWdDh02ItIvnCKT...` This is a ephemeral token (exists only temporarily) and is passed between the client, your backend and the Passwordless API. It encodes the status of an ongoing operation, such as registering a credential or signing in. You can think of it as an session or JWT token.

It's also good to understand how WebAuthn and the Passwordless API treats UserIDs, Emails, usernames, etc.

* **UserId** is a string which will be used as the [WebAuthn Userhandle](https://www.w3.org/TR/webauthn-2/#dom-publickeycredentialuserentity-id). It must **NOT contain PII** such as an email or similiar (This should be a database ID or GUID).
* **Alias** is a "human friendly" reference to a UserId. In order to allow a sign in to be initiated with a "username" of some sort (email, username, phonenumer etc), it is possible (but not required) to attach one or multiple aliases to a specific UserId. This is done with the Alias API endpoint. The Alias is hashed before storage and is only an alternative way to initiate a signin (e.g. when the UserId might not be known to the front end code initiating the sign in)

## ✨ Quick start with copy-paste <Badge text="frontend only" type="tip"/>

If you just want to **try signing in using your face/fingerprint**, you can copy-paste the demo code to your application.

[Get the code](demo-and-examples.html#copy-paste-demo-client-side-only)

## ✅ First step

To use Passwordless you need to add our library to your frontend and add a small backend integration.
Integrating Passwordless into your app or website can begin as soon as you create a Passwordless account, and requires three steps:


1. [Obtain your API keys](https://www.passwordless.dev/create-tenant/) so Passwordless can authenticate your integration’s API requests.
2. [Install the client library](https://github.com/passwordless/passwordless-client-js#get-coding) so your integration can interact with the Passwordless API and the browser
3. Register a credential to confirm everything is up and running


![Passwordless register flow](https://cdn.passwordless.dev/assets/passwordless.register.png)

<Badge text="backend" type="warning"/>
<Badge text="frontend" type="tip"/>

[Get the client-side library](https://github.com/passwordless/passwordless-client-js) from a cdn or npm.

![Passwordless sign in flow](https://cdn.passwordless.dev/assets/passwordless.signin.png?v1)


## Using Autofill / Conditional UI

The browser can present webauhtn credentials in input fields, making it easy to migrate to using passkeys.







[54-73]

sidebar: {
  '/guide/': [
    {
      title: 'Passwordless Documentation',
      collapsable: false,
      children: [
        'releasenotes',
        '',
        'getting-started',
        'api',
        'backend',
        'js-client',
        'frontend',
        'admin-console',
        'concepts',
        'plans'
      ]
    }
  ],
},
