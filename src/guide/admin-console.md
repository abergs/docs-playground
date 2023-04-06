# Admin Console

The [Admin Console](https://adminconsole-devtest.azurewebsites.net/Account/Login) is your primary GUI for creating and configuring applications, monitoring application usage, and managing billing:

<img :src="$withBase('/admin-console.png')" alt="Admin Console">

Once you create a new application, you'll have a handful of pages available to you.

## Get Started

The **Get Started** page walks you through the preliminary steps for getting passwordless.dev running in your application. This information is very similar to what's documenting in the [Get Started](get-started) guide.

## Users

The **Users** page allows you to monitor the end-users with FIDO2 WebAuthn passkeys registered for you application. For each user, as determined by their `userId`, you'll be able to see:

### Credentials

Credentials registered to each user are listed, alongside the following data points:

- Created:
- Last used:
- Credential Id:
- Public Key:
- Type:
- Counter:
- RPID:
- Origin:
- AsGuid:

### Aliases

Aliases registered to teach user are listed, however aliases that are hashed cannot be viewed here.

### Audit Logs

Audit logs track 

## Settings

The **Settings** page will offer some options for configuring your application. More to come.

## Playground

The **Playground** page gives access to a simple passwordless demo you can use for testing devices.
