# API Reference

The passwordless.dev API is used by your backend to initiate key registrations, verify signins, retrieve keys for end-users, and more.

All requests made to the passwordless.dev API **require** your API [private secret](concepts) in the header for authentication.

### API status code reference
|HTTP Code|Message|Status|
|----|----|----|
|200|Everything is OK|:green_circle:|
|201|Everything is OK but empty|:yellow_circle:|
|400|Bad request|:red_circle:|
|401|You did not identify yourself|:red_circle:|
|409|Conflict (alias is already in use)|:red_circle:|
|500|Something went very wrong and we have a bug|:red_circle:|


## /register/token

### Request

`POST` requests made to the `/register/token` endpoint create a FIDO2 WebAuthn credential for a user. The request body **must include** at least a `userId`, `username`, and `displayname`, for example:

<CodeSwitcher :languages="{http:'HTTP',js:'JavaScript'}">
<template v-slot:http>

```http
POST https://v3.passwordless.dev/register/token HTTP/1.1
ApiSecret: myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4
Content-Type: application/json

{
  "userId": "107fb578-9559-4540-a0e2-f82ad78852f7",
  "displayname": "Anders Åberg",
  "username": "anders@passwordless.dev"
}
```
</template>
<template v-slot:js>

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

</template>
</CodeSwitcher>

The request body may include additional parameters besides those required, all of which are listed here:

|Parameter        |Description          |Example Value  |
|:-------------|:------------|:-----|
|`userid`|WebAuthn userHandle. Maxium 64 bytes. Used to identify a user after succesful login.|`107fb578-9559-4540-a0e2-f82ad78852f7`|
|`displayname`|WebAuthn display name, used in UI. Never stored in database.|`Anders Åberg`|
|`username`|WebAuthn username, used in UI. Never stored in database.|`anders@passwordless.dev`|
|`attType`|WebAuthn AttestationType, can be `direct`, `indirect` and `none`. Default is `none`.|`none`|
|`userVerification`|WebAuthn AuthenticationType, can be `platform` (triggers faceid/touchid/windows hello) or `cross-platform` (triggers security-key). Default is platform.|`platform`|
|`expiresAt`|Date & time when token is set to expire, encoded using UTC ISO 8601-1:2019. Defaults to current time in utc + 120 seconds.|`2021-08-01T14:43:03Z`|

### Response

If successful, the `/register/token` endpoint will send a registration token in a `.json` response, for example:

```json
{
  "token": "wWdDh02ItIvnCKT_02ItIvn..."
}
```

This registration token will be stored by the registering device as a FIDO2 WebAuthn passkey for use in sign-in operations.

## /signin/verify

### Request
`POST` requests made to the `/signin` endpoint verify a FIDO2 WebAuthn credential. The request body **must include** a valid token, which the end-users device would have obtained on [registration](#register), for example:

<CodeSwitcher :languages="{http:'HTTP',js:'JavaScript'}">
<template v-slot:http>

```http
POST https://v3.passwordless.dev/signin/verify HTTP/1.1
ApiSecret: myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4
Content-Type: application/json

{
  "token": "wWdDh02ItIvnCKT_02ItIvn..."
}
```
</template>
<template v-slot:js>

```js
const payload = {
  "token": "wWdDh02ItIvnCKT_02ItIvn..."
};

var token = await fetch(apiurl + "/signin", {
    "method": "POST",
    "body": JSON.stringify(payload),
    "headers": { "ApiSecret": "myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4", "Content-Type": "application/json"}
});
```

</template>
</CodeSwitcher>

### Response

If successful, the `/signin/verify` endpoint will send a success response object, for example:

```json
{
  "success": true,
  "username": "anders@user.com",
  "timestamp": "2021-05-19T13:12:20.4691748Z",
  "rpid": "localhost",
  "origin": "http://localhost:3000",
  "device": "Firefox, Windows 10",
  "country": "SE",
  "nickname": null,
  "expiresAt": "2021-05-19T13:14:20.4691749Z"
}

```

## /alias

### Request

`POST` requests made to the `/alias` endpoint add aliases to a user, dictated by their `userID`, in order to allow sign-in with additional usernames, email addresses, etc. The request body **must include** the user's `userId` and a **complete** array of aliases, as pre-existing aliases are overwritten when the `POST` request is made, for example:

<CodeSwitcher :languages="{http:'HTTP',js:'JavaScript'}">
<template v-slot:http>

```http
POST https://v3.passwordless.dev/alias HTTP/1.1
ApiSecret: myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4
Content-Type: application/json

{ "UserId": "123", "aliases": ["anders@passwordless.dev", "andersbackup@passwordless.dev"]}
```
</template>
<template v-slot:js>

```js
const payload = {
    "userId": "123",
    "aliases": ["anders@passwordless.dev", "andersbackup@passwordless.dev"]
};

var token = await fetch(apiurl + "/alias", {
    "method": "POST",
    "body": JSON.stringify(payload),
    "headers": { "ApiSecret": "myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4", "Content-Type": "application/json"}
});
```
</template>
</CodeSwitcher>

A few rules to take into consideration when creating aliases:
- An alias must be unique to the specified `userId`
- An alias must be no more than 250 characters
- A `userID` may have no more than 10 aliases associated with it

### Response

 Alias are only stored as a hash to ensure user privacy, and are never returned in any API responses. If successful, the `/alias` endpoint will send a success response object, for example:

 ```

 ```

## /list
### Request

`GET` requests made to the `/list` endpoint list credentials associated with a user, as dictated by their `userId`. The request **must include** the `userId` in question, for example:

<CodeSwitcher :languages="{http:'HTTP',js:'JavaScript'}">
<template v-slot:http>

```http
GET /credentials/list?userId=USERID HTTP/1.1
ApiSecret: myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4
```
</template>
<template v-slot:js>

```js
const payload = {
    "userId": "123"
};

var credentials = await fetch(apiurl + "/credentials/list", {
    "method": "POST",
    "body": JSON.stringify(payload),
    "headers": { "ApiSecret": "myapplication:secret:11f8dd7733744f2596f2a28544b5fbc4", "Content-Type": "application/json"}
});
```
</template>
</CodeSwitcher>

### Response

If successful, the `/list` endpoint will send a success response object, for example:

```json
[
    {
        "aaGuid": "08987058-cadc-4b81-b6e1-30de50dcbe96",
        "country": "",
        "createdAt": "2021-08-01T01:29:49.7492687Z",
        "credType": "none",
        "descriptor": {
            "id": "qgB2ZetBhi0rIcaQK8_HrLQzXXfwKia46_PNjUC2L_w",
            "type": "public-key"
        },
        "device": "Firefox, Windows 10",
        "lastUsedAt": "2021-08-01T01:30:01.7528872Z",
        "origin": "http://example.com:3000",
        "publicKey": "pAEDAzkBACBZAQDK7vyAAihWxVR7lT0nlhfzVtcnlTUNRynJvUxbdu0C+R57G51MlSYhJhhv9UTx5qkyiz2nanvDX14cSqbAsCu7DjgXVVxLQT5C0QbrI8ZSdWv00Hkp5HGXpdmTTy5hHzTywaz4QwBJG92u5bwpVRkzH3C3JFI6uLt5QW5XdIG/bTqYozP8f+Gxh33ecyS9Vr4v56E3vl1+/E/dlTU8utCuoFBNjcQzocWX9XzPBMr5YfWuH2BBuiVo75US52GOIT6UQHth58Bq3ja2+E746dcCFJQoi1GN5xYru5jBQtGkBebgnmgz10QI5/a3I8MZSg7NFljccG+6nY++LY92OO6zIUMBAAE=",
        "rpid": "example.com",
        "signatureCounter": 1,
        "userHandle": "ODIzMzI2OTk2",
        "userId": "123",
        "nickname": "Home laptop"
    },
    ...
]
```

## /delete
### Request

`POST` requests made to the `/delete` endpoint delete a specific credential associated with a user, as dictated by a `credentialId`. The request **must include** the `credentialId` in question, for example:

```http
POST /credentials/delete HTTP/1.1
ApiSecret: demo:secret:yyy
Content-Type: application/json

{
    "CredentialId":"qgB2ZetBhi0rIcaQK8_HrLQzXXfwKia46_PNjUC2L_w"
}
```

### Response

If successful, the `/delete` endpoint will send a success response object, for example:

```JSON

```

## /account/delete
### Request

`POST` requests made to the `/account/delete` endpoint will delete your passwordless.dev account and all data stored in it. Data **will not be deleted immediately**, instead:

1. Admin emails associated with the account will receive a warning email with a link to abort the deletion process.
2. After 24 hours your API keys will be frozen.
3. After 14 days your data will be permanently deleted.

Requests must only include your API private secret, for example:

```http
POST /account/delete HTTP/1.1
ApiSecret: demo:secret:yyy
```

### Response

If successful, the `/account/delete` endpoint will send a success response object, for example:

```json

```

<!-- ### Register - Begin

Internal - This API is used by the Passwordless Client JS library

```http
POST https://v3.passwordless.dev/register/begin HTTP/1.1
ApiKey: demo:public:6b08891222194fd1992465f8668f
Content-Type: application/json

{"token":"P-lP40PXvYzanr5BpaR14A:_UeZDzlAjl-TDLd1MK-OMO4AethFV2flVejX06Z_CyUCaijmRGjOoBVrRX4kkRCY","RPID":"localhost","Origin":"http://localhost:3000"}
```

Response:

```json
{
  "data": {
    "rp": { "id": "localhost", "name": null },
    "user": { "name": "test_user", "id": "dGVzdF91c2Vy", "displayName": null },
    "challenge": "9jouMw-UgSis_lWlKcpn5Q",
    "pubKeyCredParams": [
      { "type": "public-key", "alg": -7 },
      { "type": "public-key", "alg": -257 },
      { "type": "public-key", "alg": -37 },
      { "type": "public-key", "alg": -35 },
      { "type": "public-key", "alg": -258 },
      { "type": "public-key", "alg": -38 },
      { "type": "public-key", "alg": -36 },
      { "type": "public-key", "alg": -259 },
      { "type": "public-key", "alg": -39 },
      { "type": "public-key", "alg": -8 }
    ],
    "timeout": 60000,
    "attestation": "none",
    "authenticatorSelection": {
      "requireResidentKey": false,
      "userVerification": "preferred"
    },
    "excludeCredentials": [],
    "extensions": {
      "exts": true,
      "uvi": true,
      "loc": true,
      "uvm": true,
      "biometricPerfBounds": { "FAR": 3.4028235e38, "FRR": 3.4028235e38 }
    },
    "status": "ok",
    "errorMessage": ""
  },
  "sessionId": "ZbiFQ1QLgVBVa5N2whoHyg:ByTuwQF1rnkYincVeA4P_7pg00NgUxw89gg-C1kPJob_E2-uI6akvd6qfZ4fS1f8pj5n274D7b0RNTj13Ln69YJNy50ZneTSSOY354Ps_gxBLQnS9XNscK3u-lnYDeZ56VFEMnnZBrEmc_TMtLgJHdh6nZpUsTT0HH69NXbl0em4KNCSJVrk92ouZxTJq8YGI5VbfwXu6zfzyBObjxcdhRKd4UBSEWCZT0OV880KWvBtxFaay7E05H4kif20Au_4mHEvzAJs5oo9jPCIHwyrn-a0g9FyR-EapOCu8jmvxd6AHKMV8eOyGtNceYTSxEuyvaG8BhAPPvdC5GidAIGaeVTsf4ol2y-IzrngLnBsEY8JO7ysSCDzHrqQ0p8pdvxhuUKPBpG5AxTm1FULaY9PNKVH2HNhXQ7KDY8OFgCvB1zcB8PqCgi8MPeFKn-jkwkEsvXYJM8Q4SKagBqSn_H6CfHkhd3VkJYGJBqL9wMYLfju5rAaXFOcR6-NjfaPliNdjv57-xg8ffMvTvpNf-mpKwe8kT9HxbGhbkU48A_h7ermMidDHST1XCmlM8AhT8UiDFaGoyy2-LVzXnllQT9uiAiFdehMc26Y5_7jJczZcfSkuw_DP8H_P-0riW6EhHH8VGY7Rb4JHyrXhXE2KFQPv7Gcgf3DsD_WhzIZRZOY-M5tNWSbIwk-uA99D9Bb8zCKkdGLaEwwI93QsLZ7Wl2nRKQnKijhb6vg21PmqXHgIcJfvFWFMBrndPLVU7z67Q2CzLgwUKaTnkvBU7LMCICs9_5BV1SCVTMZlYarvpwA6TriW9Hg2SDiucIGgCAjMqW8Pre_rVpbRqOvqosur3LyHcHjoLsytwMqB8s3PUGoehKmiFlBds3g01CGW5K0-Uck"
}
```

### Register - Complete

Internal - This API is used by the Passwordless Client JS library

```http
POST https://v3.passwordless.dev/register/complete HTTP/1.1
ApiKey: demo:public:6b08891222194fd1992465f8668f
Content-Type: application/json

{
   "response":{
      "id":"M_HGNhr1ELH45hlEpVEE-Uek2YQOC_9_fmAS1yWsfH8",
      "rawId":"M_HGNhr1ELH45hlEpVEE-Uek2YQOC_9_fmAS1yWsfH8",
      "type":"public-key",
      "extensions":{

      },
      "response":{
         "AttestationObject":"o2NmbXRkbm9uZWdhdHRTdG10oGhhdXRoRGF0YVkBZ0mWDeWIDoxodDQXD2R2YFuP5K65ooYyx5lc87qDHZdjRQAAAAAAAAAAAAAAAAAAAAAAAAAAACAz8cY2GvUQsfjmGUSlUQT5R6TZhA4L_39-YBLXJax8f6QBAwM5AQAgWQEAxg1pNtQU3wuOg5X9Rbz5ofVlBD0hD2qQojpxx2_fPi89bd21DHyTNA2TDLLtu4czINYf7cbBU07I8_WY-sbDtQwHV38MvzI5dwaoa18F1InzOm5j2q7eYe-irBDB8-92G5FME6_rj11dYyjbx6nK2Tt9M2EkBKXNxyMGrowkW2CLMDVxeOaH8IyqJYWILM1R6eNrOk2TBczTO83zNE6rQN7pkZHPF1zsC5YRnpA32obkZQU-i4-Lubp5kV_64yy5kIIugog3O8CVSn43NNxukYG5r6VqD4C0By0rPqEJBm0F3WepP0I4M1rg7cVKCUK-GMwYR11drhzwnuxWY2MuDSFDAQAB",
         "clientDataJson":"eyJ0eXBlIjoid2ViYXV0aG4uY3JlYXRlIiwiY2hhbGxlbmdlIjoiOWpvdU13LVVnU2lzX2xXbEtjcG41USIsIm9yaWdpbiI6Imh0dHA6Ly9sb2NhbGhvc3Q6MzAwMCIsImNyb3NzT3JpZ2luIjpmYWxzZX0"
      }
   },
   "sessionId":"ZbiFQ1QLgVBVa5N2whoHyg:ByTuwQF1rnkYincVeA4P_7pg00NgUxw89gg-C1kPJob_E2-uI6akvd6qfZ4fS1f8pj5n274D7b0RNTj13Ln69YJNy50ZneTSSOY354Ps_gxBLQnS9XNscK3u-lnYDeZ56VFEMnnZBrEmc_TMtLgJHdh6nZpUsTT0HH69NXbl0em4KNCSJVrk92ouZxTJq8YGI5VbfwXu6zfzyBObjxcdhRKd4UBSEWCZT0OV880KWvBtxFaay7E05H4kif20Au_4mHEvzAJs5oo9jPCIHwyrn-a0g9FyR-EapOCu8jmvxd6AHKMV8eOyGtNceYTSxEuyvaG8BhAPPvdC5GidAIGaeVTsf4ol2y-IzrngLnBsEY8JO7ysSCDzHrqQ0p8pdvxhuUKPBpG5AxTm1FULaY9PNKVH2HNhXQ7KDY8OFgCvB1zcB8PqCgi8MPeFKn-jkwkEsvXYJM8Q4SKagBqSn_H6CfHkhd3VkJYGJBqL9wMYLfju5rAaXFOcR6-NjfaPliNdjv57-xg8ffMvTvpNf-mpKwe8kT9HxbGhbkU48A_h7ermMidDHST1XCmlM8AhT8UiDFaGoyy2-LVzXnllQT9uiAiFdehMc26Y5_7jJczZcfSkuw_DP8H_P-0riW6EhHH8VGY7Rb4JHyrXhXE2KFQPv7Gcgf3DsD_WhzIZRZOY-M5tNWSbIwk-uA99D9Bb8zCKkdGLaEwwI93QsLZ7Wl2nRKQnKijhb6vg21PmqXHgIcJfvFWFMBrndPLVU7z67Q2CzLgwUKaTnkvBU7LMCICs9_5BV1SCVTMZlYarvpwA6TriW9Hg2SDiucIGgCAjMqW8Pre_rVpbRqOvqosur3LyHcHjoLsytwMqB8s3PUGoehKmiFlBds3g01CGW5K0-Uck",
   "RPID":"localhost",
   "Origin":"http://localhost:3000"
}
```

### Sign in - Begin

Internal - This API is used by the Passwordless Client JS library

```http
POST https://v3.passwordless.dev/signin/begin HTTP/1.1
ApiKey: demo:public:6b08891222194fd1992465f8668f
Content-Type: application/json

{"username":"test_user","RPID":"localhost","Origin":"http://localhost:3000"}
```

Response:

```json
{
  "data": {
    "challenge": "Xt8jaU-dj2nCCMznu6G8Hw",
    "timeout": 60000,
    "rpId": "localhost",
    "allowCredentials": [
      {
        "type": "public-key",
        "id": "M_HGNhr1ELH45hlEpVEE-Uek2YQOC_9_fmAS1yWsfH8"
      }
    ],
    "userVerification": "discouraged",
    "extensions": {
      "txAuthSimple": "FIDO",
      "txAuthGenericArg": {
        "contentType": "text/plain",
        "content": "RklETw=="
      },
      "uvi": true,
      "loc": true,
      "uvm": true
    },
    "status": "ok",
    "errorMessage": ""
  },
  "sessionId": "zqlqr8EsuR9QVcDWwTZhHA:_1QW8x_v5E7HnnnBe_2Rd4oBmrG-Ogc-jA6l4P3AZfqxiKq22Dj3fGm7Y_4SrR4eD00PTllNT9DPYsQCanSNpZNx_hfhDGltfxC7VBoSU73yWy55wDj2929St1Jtea3PJN0ZOczlY84Ws_q1ewRd0-zj4fXArpJ6jOiQu-i48yAbdyc18wXJxDxkVXv6w5cAMhHMKQs4c0oaJwR9-0Cr0knCQ9aZan-7gsnMzt05wGmfZ1L5VPcvHMSAxAeAYB8IGgctThluXxMsR0kZpVsPTrV2pOwGp-01VVDU60qOngRs5F4RkrMClUxj2ok3y3lStgMEngUMdRg"
}
```

### Sign in - Complete

Internal - This API is used by the Passwordless Client JS library

```http
POST https://v3.passwordless.dev/signin/complete HTTP/1.1
ApiKey: demo:public:6b08891222194fd1992465f8668f
Content-Type: application/json

{
   "response":{
      "id":"M_HGNhr1ELH45hlEpVEE-Uek2YQOC_9_fmAS1yWsfH8",
      "rawId":"M_HGNhr1ELH45hlEpVEE-Uek2YQOC_9_fmAS1yWsfH8",
      "type":"public-key",
      "extensions":{

      },
      "response":{
         "authenticatorData":"SZYN5YgOjGh0NBcPZHZgW4_krrmihjLHmVzzuoMdl2MFAAAAAQ",
         "clientDataJson":"eyJ0eXBlIjoid2ViYXV0aG4uZ2V0IiwiY2hhbGxlbmdlIjoiWHQ4amFVLWRqMm5FSU16bnU2RzhIdyIsIm9yaWdpbiI6Imh0dHA6Ly9sb2NhbGhvc3Q6MzAwMCIsImNyb3NzT3JpZ2luIjpmYWxzZX0",
         "signature":"FWqjYDCZvVYMWctEFmeekaCnRs7kiWJLRMrVcOM02UHO3vT1wkAqn6iusaGQGDXQkwJvGWMgkDeF8BMQHfyRmB_MK4mMxrgAHnBxUbiMsL7Ld01chQZia2_4G8wkU3a0OvaFPduXRE1ahzpmctAJF70jmpv6O3rvlif-CL1kVkeXRLeqA2nwYX2ogfbQMtzYNmcETM4QG5S8JZkczitESOX4R1UVScqC4051zMgfZAElaVVjrb8574bYggPp-vlA18-DUvumTaQKfiQquZVaYCieAw2Trwkt_bxaHCZ1rU8Po2KbiViy2F9TDBJIAV6iDPo951IJ3L5I21k_i2WcbA"
      }
   },
   "sessionId":"zqlqr8EsuR9QVcDWwTZhHA:_1QW8x_v5E7HnnnBe_2Rd4oBmrG-Ogc-jA6l4P3AZfqxiKq22Dj3fGm7Y_4SrR4eD00PTllNT9DPYsQCanSNpZNx_hfhDGltfxC7VBoSU73yWy55wDj2929St1Jtea3PJN0ZOczlY84Ws_q1ewRd0-zj4fXArpJ6jOiQu-i48yAbdyc18wXJxDxkVXv6w5ch0vv9GjPD_HXSf5mTsO2pQKstZuXzPSsvTF72cVPsCJ8ZAfOFpxf28e-qLDyuklfVt4G87uY847AQkNySRdCuaA3KABvzgLTzDwC7tmnfHc90bMYnNsTA2a3XBbil8gtaEneRCURYFH7N-b12qEBihd3-nHkRd6Dkz2H3i_o1VDMtkmpC55jv6Y87xKJk9eexKGbXLnbi4su8RNPeuCrF5AurNsSy5XBaHcrbUfcDhyxxBHEPXGV6uilm4OHCCxqYvvOuH4P2DLAMhHMKQs4c0oaJwR9-0Cr0knCQ9aZan-7gsnMzt05wGmfZ1L5VPcvHMSAxAeAYB8IGgctThluXxMsR0kZpVsPTrV2pOwGp-01VVDU60qOngRs5F4RkrMClUxj2ok3y3lStgMEngUMdRg",
   "RPID":"localhost",
   "Origin":"http://localhost:3000"
}
```

Response:

```json
{
  "data": "zDM3wmUMaXjKole4EUKYbw:kSmJNcfcGiAxsHmEb2VPwBPc9hsMlvGg5HJS4zIjmnKbMvRj4oEIy_Ld9vZHoGW7KoGZAL9zDVGuDAQYcO4eiCIvjcGb2Oq-40svSgWWOVClG9UfXw7UaSlwMkBlG5Op"
}
``` -->
