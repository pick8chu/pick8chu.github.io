---
title: PKCE
last_updated: Aug 14, 2025
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: pkce.html

---


<img width="539" height="378" alt="image" src="https://github.com/user-attachments/assets/96a32782-9bdc-4dd5-94cb-f0f5f6fa2ed6" />


### Filled-in flow (server exchange with PKCE)

- 1) App → Provider: Authorization request (in browser)

- response_type=code

- client_id

- redirect_uri

- scope=openid email profile

- state

- code_challenge

- code_challenge_method=S256

- nonce (recommended)

- 2) Provider → App: Redirect back to your redirect_uri

- code

- state

- error/error_description (if failed)

- 3) App → Backend: Exchange request (your endpoint, e.g. /auth/google-token-exchange)

- code

- codeVerifier

- clientId

- redirectUri

- provider ("GOOGLE" | "APPLE" | "FACEBOOK")

- nonce (if you used one, so BE can validate it)

- Do NOT send subId; BE will get it from the verified id_token.

- 4) Backend → Provider (token endpoint)

- grant_type=authorization_code

- code

- client_id

- redirect_uri

- code_verifier

- 5) Provider → Backend: Token response

- id_token (JWT with claims: sub, iss, aud, exp, nonce, email, etc.)

- access_token

- token_type

- expires_in

- refresh_token (sometimes, if offline access requested)

- Backend actions after step 5

- Verify id_token signature via JWKS and validate iss/aud/exp[/nonce].

- Extract sub (and email).

- Find/create user by (subId, provider).

- Issue your own JWTs (access/refresh) and return to the app.

Notes:

- The app never sends subId; it’s derived server-side from the verified id_token.

- PKCE prevents a stolen authorization code from being redeemed without the one-time code_verifier.

- JWT secure storage protects tokens after issuance; PKCE protects the exchange before issuance.

Summary

- Provider → App returns code and state, not subId.

- App → Backend sends code, codeVerifier, clientId, redirectUri (and provider/nonce).

- Provider → Backend returns id_token/access_token; BE verifies id_token, extracts sub, then mints your JWTs.
