## Register a credential

Registering a credential is the final step to complete the Passwordless.dev integration. 
The integration process consists of:
1. Obtain your API keys [link]
2. install the client library [link]
3. Register a credential to confirm everything is up and running. 

If you have not compeleted all of the proper steps in order, the links will guide you to the appropriate sections of the help center. 

## Credential workflow 

[image location]

The chart provides an illustration of the credential workflow with Passwordless.dev. 

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


## Sign-in flow

[diagram locaiton]

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
