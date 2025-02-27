## 1. Initiating the OAuth Flow

To start the OAuth flow, redirect the user to our authorization endpoint:

```
GET /oauth/authorize
```

### Query Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `client_id` | Yes | Your client ID |
| `redirect_uri` | Yes | URI to redirect to after authorization (must be pre-registered) |
| `scope` | Yes | Space-separated list of scopes (e.g., "miles:read miles:write") |
| `state` | No | Random string to prevent CSRF attacks |
| `response_type` | Yes | Must be "code" |
| `code_challenge` | Yes | PKCE code challenge |
| `code_challenge_method` | Yes | Must be "S256" |

### PKCE Implementation

For PKCE, you need to:

1. Generate a random `code_verifier` (between 43-128 characters)
2. Create a `code_challenge` by hashing the verifier with SHA-256 and base64url-encoding it:

```javascript
// Example in JavaScript
function generateCodeVerifier() {
  const array = new Uint8Array(32);
  window.crypto.getRandomValues(array);
  return base64UrlEncode(array);
}

function generateCodeChallenge(codeVerifier) {
  const encoder = new TextEncoder();
  const data = encoder.encode(codeVerifier);
  return window.crypto.subtle.digest('SHA-256', data)
    .then(hashBuffer => {
      const hashArray = Array.from(new Uint8Array(hashBuffer));
      return base64UrlEncode(hashArray);
    });
}

// Base64 URL encode (RFC 4648)
function base64UrlEncode(array) {
  return btoa(String.fromCharCode.apply(null, array))
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=+$/, '');
}
```

### Example Request

```
GET /oauth/authorize?
  client_id=your_client_id&
  redirect_uri=https://your-app.com/callback&
  scope=miles:read%20miles:write&
  state=random_state_string&
  response_type=code&
  code_challenge=CODE_CHALLENGE&
  code_challenge_method=S256
```

### Response

If successful, the API will return a JSON response with:

```json
{
  "message": "Authorization code created successfully",
  "client_id": "your_client_id",
  "scopes": ["miles:read", "miles:write"],
  "redirect_uri": "https://your-app.com/callback",
  "state": "random_state_string"
}
```

## 2. Handling User Consent

After the user approves the authorization, you need to handle the consent:

```
POST /oauth/consent
```

### Request Body

```json
{
  "client_id": "your_client_id",
  "approve": true,
  "redirect_uri": "https://your-app.com/callback",
  "state": "random_state_string"
}
```

### Response

If approved, the API will return:

```json
{
  "code": "authorization_code",
  "redirect_uri": "https://your-app.com/callback",
  "state": "random_state_string"
}
```