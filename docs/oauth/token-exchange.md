## 3. Exchanging the Authorization Code

Exchange the authorization code for access and refresh tokens:

```
POST /oauth/token
```

### Request Body

```json
{
  "grant_type": "authorization_code",
  "code": "authorization_code",
  "client_id": "your_client_id",
  "client_secret": "your_client_secret",
  "code_verifier": "your_code_verifier"
}
```

### Response

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "refresh_token_value",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "miles:read miles:write"
}
```

## 4. Refreshing an Access Token

When the access token expires, you can obtain a new one using the refresh token:

```
POST /oauth/token
```

### Request Body

```json
{
  "grant_type": "refresh_token",
  "refresh_token": "refresh_token_value",
  "client_id": "your_client_id"
}
```

### Response

```json
{
  "access_token": "new_access_token",
  "refresh_token": "new_refresh_token",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "miles:read miles:write"
}
```

## Security Considerations

1. Always use HTTPS for all OAuth requests
2. Store tokens securely and never expose them to the frontend
3. Validate all redirect URIs
4. Implement PKCE for all authorization flows
5. Use the state parameter to prevent CSRF attacks