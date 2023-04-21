# About passwordless.dev

Passwordless.dev is a software toolkit that helps web developers to support [FIDO2 WebAuthn passkeys](concepts.html#fido2) for authentication by their end-users. Using passwordless.dev means there's no need to read ponderous W3C specification documentation, no need to determine what cryptography to implement, and no need to worry about managing stored public keys; the team behind Bitwarden does these things for you.

Passwordless.dev wraps FIDO2 WebAuthn passkey functionality in an easy-to-use tools, designed to make it easier for web developers to adopt passkey-based authentication to meet the challenges of an ever-shifting cybersecurity landscape.

[INSERT DIAGRAM HERE]

Architecturally, passwordless.dev consists of three key parts:

- An [open-source client side library](js-client), used by your frontend to make requests to end-users browsers' WebAuthn API and requests to the passwordless.dev APIs.
- A public RESTful API, used by the client-side library to complete FIDO2 WebAuthn cryptographic exchanges with the browser.
- A [private RESTful API](api), used by your backend to initiate key registrations, verify signins, and retrieve keys for end-users.

::: tip
For more information on the guts of passwordless.dev check out [Concepts](concepts), otherwise we recommend heading straight to [Get Started](get-started).
:::
