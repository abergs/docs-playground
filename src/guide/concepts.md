# Concepts

## What is FIDO2?
FIDO2 is the world wide web consortium standard's (W3C) specification for web authentication (WebAuthn), and client to authenticator protocol (CTAP). FIDO authentication standards were developed in order to provide authentication that is more secure than standard passwords and SMS 2FA. Using FIDO authentication standards can provide a secure experience that is simpler for consumers to use and developers to implement. Learn more about FIDO at [FIDO Alliance](https://fidoalliance.org/fido2/).

FIDO2 consists of two standardized components, **WebAuthn** and **CTAP**. Together, these standards operate to create a secure and passwordless experience. 

* **WebAuthn** is an API that connects a relying party to an application or login system. In a practical sense, WebAuthn creates an easy connection between the web and an application to allow passwordless authentication to occur. Learn more about WebAuthn at [Yubico](https://www.yubico.com/resource/why-webauthn-matters/).
* **CTAP2** The client to authenticator protocol components allow an external and portable authenticator (security key) to operate with a client platform. FIDO CTAP2 is responsible for the external factor, like a security key, communicating with a website or account via an authenticator.

In order to acheive FIDO2 compliance, the authentication process will incormporate both Webathn and CTAP2 standards.

## Components of the Passwordless.dev API
The Passwordless API utilizes three types of tokens:
* **ApiKey** ```example:public:6b0861e..``` This is a Public API key, safe and intended to be included client side. It allows the browser to connect to out backend and initiate key negotiations and assertions.
* **ApiSecret** ```example:secret:4fd1992..``` This is a Secret API key or private secret, and should be well protected. It allows your backend to verify sign-ins and register keys on behalf of your users. (Create an account to get your API keys).
* **Token** ```wWdD02ItIvnCKT...``` This is a ephemeral token (exists only temporarily) and is passed between the client, your backend and the Passwordless API. It encodes the status of an ongoing operation, such as registering a credential or signing in. You can think of it as a session or JSON web token (JWT).

## User information
* The **Userid** is a unique string that represents the [WebAuthn Userhandle](https://www.w3.org/TR/webauthn-2/#dom-publickeycredentialuserentity-id). The userid is not meant to be displayed to a user and does not contain personally identifieable information (such as email, name). Authentication attempts are made with the userid and not individual names or display names. 
* An **alias** is a user-facing reference to a userid. An alias is assigned to a userid for login purposes (username, email). Multiple alias' can be connected to a userid with the [Alias API](/api.md) endpoint.

### Passkey
Based on FIDO standards, passkeys are an alternitive to passwords, which provide faster, easier, and more secure sign-ins to websites and apps. Device-bound passkeys can operate on platforms across a user's device. Learn more about passkeys [here](https://fidoalliance.org/passkeys/).

### Security key
A security key is an effective and popular method of adding two-factor authentication (2FA) to an account. A security key is a USB-like physical device that will store and handle cryptography. Security keys are based on FIDO2 authentication standards. 
See [Yubico](https://www.yubico.com/products/security-key/) to learn more about security keys. 

### Relying party
The relying party (RP) is the server that process requests for access to a resource. A web application that verifies a users credentials during an access request would be an example of a RP. 

### Relying party ID
The ID for relying parties provides the technology platform an identification that corresponds with the given domain. 

### User verification 
A FIDO2 server RP can interact with an authenticator to verify a user. This can be done via PIN code, biometrics, or other 2FA methods that securely verify that the proper person is accessing an account. 

### Platform vs. cross-platform
An authenticator can have two classifications:
* A **platform authenticator** is built-in or part of the client platform (such as Faceid, TouchID, Windows Hello,).
* A **roaming authenticator** ("cross-platform") is detachable (security key).


# Registering and signing in

To better understand how Passwordless.dev works, we have provided illustrations and explinations of the registration and signin processes.

## Credential workflow 

![Passwordless Signing](/passwordless.register.png)

The chart provides an illustration of the credential registration workflow with Passwordless.dev. Here is how the steps occur:

1. Your backend will make a call to the Passwordless.dev API ```/register/token``` endpoint with the ```username/id``` of the user. 
```
POST https://v3.passwordless.dev/register/token
ApiSecret: demo:secret:yyy
Content-Type: application/json

{ "UserId": "123", "username": "Jsmith@passwordlessuser.com", "displayName": "Mr. Joe Smith" }
```
Response ```200 OK```

```
"register_wWdDh02ItIvnCKT_02ItIvn..."
```


2. The client-side initaites the WebAuthn process. The credentials are now stored with the Passwordless.dev API via the token.
The client-side library can be retreived [here](https://github.com/passwordless/passwordless-client-js) through cdn or npm.
Your client-side code will now start the registration process. WebAuthn will allow keys to be stored in the Passwordless.dev API. 
```
var p = new Passwordless.Client({
    apiKey: "demo:public:6b08891222194fd1992465f8668f"
});

// register_wWdDh02ItIvnCKT_02ItIvn...
var myToken = await fetch("/example-backend/passwordless/token").then(r => r.text());

try {
    await p.register(myToken);
    // success!
} catch (e) {
    // error    
}
```

# How it works

## Sign-in flow

![Sign in](/passwordless.signin.png)

The chart provides an illustration of the sign in process with Passwordless.dev. Here is how the steps occur:

1. Start the Passwordles sign in
Retrieve the clience-side library [here](https://github.com/passwordless/passwordless-client-js) from cdn or npm. Pass an alias or id to the sign in method to begin the WebAuthn process. 
```
var p = new Passwordless.Client({
    apiKey: "demo:public:6b08891222194fd1992465f8668f"
});

var alias = "John@user.com"; // get username from input

// returns verify_yUf6_wWdDh02ItIvnCKT_02ItIvn...
var token = await p.signinWithAlias(alias);
// var token = await p.signinWithConditional(); // Uses what is known as the Conditional UI to sign in using autocomplete in the browser
// var token = await p.signinWithId("123"); // if you did not set an alias, you can signin with the UserId.

// verify the token
var verifiedUser = await fetch("/example-backend/passwordless/signin?token=" + token).then(r => r.json());
if(verifiedUser.success === true) {
    //success!
}
```

2. Once the client-side code has finished the WebAuthn process, the token needs to be verified with the backend API. Once compelted, the WebAuthn process can succeed and identify what user has signed in.

```
POST https://v3.passwordless.dev/signin/verify
ApiSecret: demo:secret:yyy
Content-Type: application/json

{ "token": "verify_yUf6_wWdDh02ItIvnCKT_02ItIvn..." }
```

Response:

```
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

Once a successful response has been recoreded you are done! For additional information on the API and other endpoints, see [here].








