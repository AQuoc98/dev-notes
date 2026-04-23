# Authentication & Authorization

**Status:** 🚧 - **Last Updated:** 23rd Apr 2026

- [Authentication \& Authorization](#authentication--authorization)
  - [Authentication methods](#authentication-methods)
  - [Questions](#questions)
    - [✅ **1. What the difference between Refresh Token and Access Token?**](#-1-what-the-difference-between-refresh-token-and-access-token)

## Authentication methods
- [Basic](https://roadmap.sh/guides/basic-authentication)
- [Session Based Authentication](https://roadmap.sh/guides/session-based-authentication)
- [Token Based Authentication](https://roadmap.sh/guides/token-authentication)
- [Jwt Authentication](https://roadmap.sh/guides/jwt-authentication)
- [OAuth](https://roadmap.sh/guides/oauth)
- [SSO](https://roadmap.sh/guides/sso)

## Questions

### ✅ **1. What the difference between Refresh Token and Access Token?**

| Aspect              | Access Token                                   | Refresh Token                                         |
|---------------------|------------------------------------------------|-------------------------------------------------------|
| Purpose             | Grants access to protected resources (APIs)    | Used to obtain a new access token when it expires     |
| Lifetime            | Short-lived (5 min – 1 hour typical)           | Long-lived (days, weeks, or months)                   |
| Sent to             | Resource server (every API call)               | Only the auth/token server (e.g., `/oauth/token`)     |
| Frequency of use    | Very frequent (per request)                    | Rare (only on expiry / re-auth)                       |
| Format              | Usually a JWT (self-contained, stateless)      | Usually an opaque random string stored server-side    |
| Storage (client)    | In memory or HTTP-only cookie                  | HTTP-only, Secure, SameSite cookie (safest)           |
| Storage (server)    | Not stored (stateless JWT)                     | Stored in DB so it can be revoked/rotated             |
| Validation          | Signature + expiry check (no DB hit)           | DB lookup to check validity & revocation              |
| Transmission        | `Authorization: Bearer <token>` header         | Body of the refresh request, or HTTP-only cookie      |
| Exposure risk       | Higher — travels with every request            | Lower — sent only to one endpoint                     |
| Revocation          | Hard (wait for expiry or use a blacklist)      | Easy (delete/flag the record in DB)                   |
| Scope / claims      | Carries user ID, roles, permissions, `exp`     | Carries only an identifier to look up the session     |
| Rotation            | Not rotated — just reissued                    | Should be rotated on each use (refresh token rotation)|
| If stolen           | Attacker has access until expiry               | Attacker can mint new access tokens until revoked     |

**Typical flow**

1. User logs in → server returns **access token** (short) + **refresh token** (long).
2. Client calls APIs with `Authorization: Bearer <access_token>`.
3. When the access token expires (`401`), the client silently POSTs the refresh token to `/token/refresh`.
4. Server validates, **rotates** the refresh token (issues a new one, invalidates the old), and returns a new access token.
5. On logout, the server deletes the refresh token record.

**Security best practices**

- Keep access tokens **short-lived** (≤ 15 min).
- **Rotate** refresh tokens on each use and detect reuse (a reused old token = theft → revoke the whole family).
- Store refresh tokens in **HTTP-only, Secure, SameSite=Strict/Lax cookies**, never in `localStorage`.
- Bind tokens to a device/client fingerprint where possible.
- Always serve over HTTPS.

