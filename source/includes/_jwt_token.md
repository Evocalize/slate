# JWT Access Tokens

## JWT Basics

The primary technology/concept used for authentication between our two systems is a JSON Web Token. The following link has documentation that you may find useful.

- https://jwt.io/ - Documentation on all things JWT. On this site, you can learn all there is to
know about the standard, libraries to use, test out the tokens you generate, etc.

To create a valid JWT you will need to sign it with your evocalize `jwt_secret` using HS256 (HMAC with SHA-256).
We will provide the `jwt_secret` value to you.

## Variables you provide to Evocalize

### Login URLs

You will need to provide us with Login URLs to send a user to if they arrive at the site unauthenticated or
their login expires. Typically, we recommend having both a staging and a production endpoint when integrating.

## SSO Endpoints for Evocalize Web App

This is where you will redirect your users after generating a JWT for them on your SSO page. See below for information you need to pass to this endpoint.

- Staging: https://your-staging-white-label-domain.evocalize.com/#/sso?token=<...>
- Production: https://your-white-label-domain.com/#/sso?token=<...>

## Authentication Flow

> JWT Auth flow pseudo code example

```javascript

// 0. Authenticate the user in your system to verify they are who they say they are.
user.authenticate();

// 1. Generate a JWT with the "sub" set to include the userId (same ID passed to Evocalize in API calls or feed files)
// note: `jwt_secret` will be provided by Evocalize
// here is an example JWT payload that we need you to generate:
const userJwtBlob = {
    sub: 'yourOrgSlug|' + user.id, // where yourOrgSlug is the value we provide you with and user.id with the ID you send in API calls or feed files.
    iat: 1563831852, // "Issued At" (seconds since Unix Epoch)
    exp: 1563835452 // "Expires At" (seconds since Unix Epoch) set to 1 hour after iat
};

const jwt = generateJwt(userJwtBlob, jwtSecret);

const path = encodedURIComponent("/#/dashboard/gallery") // any URL-encoded path will do

// 2. Redirect the user to a new Evocalize SSO endpoint with the JWT
redirectUser('https://your-white-label-domain.com/#/sso?token=' + jwt + 'path=' + path);

```

You will need to create a new SSO endpoint (or update your current one) to generate a
token for your users using a secret key provided by Evocalize. You can then send your
users to a specific endpoint to get them seamlessly authenticated in our application.
The following workflow shows an example end-to-end process. Some of it is in pseudo
code to keep things succinct. If any portions require clarity, please reach out to us, and
we can assist you further.

### Flow
- User navigates to your new (or existing) SSO endpoint.
- User signs in (or is already authenticated).
- Generate a JWT (JavaScript example on the right).
- Redirect the user to the `/sso` endpoint, providing the JWT as the value for
  the `token` query parameter.
- If successful, the user is authenticated, and the Evocalize CMP UI loads.
  If an error or some failure occurs, then an error screen is displayed with a "try again"
  button that loads the `/sso` endpoint again when it is clicked.
  
