# Integration Guide

This guide will help you integrate your application with our OAuth and Partner API.

## Prerequisites

Before you begin:

1. Register as a partner and obtain your client credentials:
   - `client_id`
   - `client_secret`
   - Allowed redirect URIs

2. Ensure you have the required scopes:
   - `miles:read` - For reading user miles data
   - `miles:write` - For awarding points and processing transactions

## Integration Steps

### 1. Implement the OAuth Flow

#### Backend Setup

Create endpoints on your server to:

1. Generate PKCE code verifiers and challenges
2. Store the code verifier securely
3. Handle the OAuth callback and token exchange

#### Frontend Implementation

Add a "Connect" button in your app that redirects users to our authorization endpoint:

```javascript
function connectToMilesApp() {
  // Generate PKCE code verifier and challenge
  const codeVerifier = generateCodeVerifier();
  
  // Store code verifier in session storage
  sessionStorage.setItem('code_verifier', codeVerifier);
  
  // Generate code challenge from verifier
  generateCodeChallenge(codeVerifier).then(codeChallenge => {
    // Build authorization URL
    const authUrl = new URL('https://your-api-domain.com/oauth/authorize');
    authUrl.searchParams.append('client_id', 'your_client_id');
    authUrl.searchParams.append('redirect_uri', 'https://your-app.com/callback');
    authUrl.searchParams.append('scope', 'miles:read miles:write');
    authUrl.searchParams.append('state', generateRandomState());
    authUrl.searchParams.append('response_type', 'code');
    authUrl.searchParams.append('code_challenge', codeChallenge);
    authUrl.searchParams.append('code_challenge_method', 'S256');
    
    // Redirect to authorization page
    window.location.href = authUrl.toString();
  });
}
```

#### Handling the Callback

Create a callback endpoint on your server to exchange the authorization code for tokens:

```javascript
app.get('/callback', async (req, res) => {
  const { code, state } = req.query;
  
  // Verify state parameter
  if (state !== req.session.oauth_state) {
    return res.status(400).send('Invalid state parameter');
  }
  
  // Retrieve code verifier from session
  const codeVerifier = req.session.code_verifier;
  
  try {
    // Exchange code for tokens
    const response = await axios.post('https://your-api-domain.com/oauth/token', {
      grant_type: 'authorization_code',
      code,
      client_id: 'your_client_id',
      client_secret: 'your_client_secret',
      code_verifier: codeVerifier
    });
    
    // Store tokens securely
    req.session.access_token = response.data.access_token;
    req.session.refresh_token = response.data.refresh_token;
    req.session.token_expiry = Date.now() + (response.data.expires_in * 1000);
    
    // Redirect to success page
    res.redirect('/connection-success');
  } catch (error) {
    console.error('Token exchange error:', error.response?.data || error.message);
    res.redirect('/connection-error');
  }
});
```

### 2. Making API Requests

Once you have obtained an access token, you can use it to make requests to the Partner API:

```javascript
async function awardPoints(userId, categoryId, pointsToAward) {
  try {
    const response = await axios.post('https://your-api-domain.com/partner-rewards/award-points', {
      userId,
      categoryId,
      pointsToAward
    }, {
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    });
    
    return response.data;
  } catch (error) {
    console.error('Error awarding points:', error.response?.data || error.message);
    throw error;
  }
}
```

### 3. Token Refresh

Implement token refresh functionality to ensure your application maintains access:

```javascript
async function refreshAccessToken() {
  try {
    const response = await axios.post('https://your-api-domain.com/oauth/token', {
      grant_type: 'refresh_token',
      refresh_token: refreshToken,
      client_id: 'your_client_id'
    });
    
    // Update stored tokens
    accessToken = response.data.access_token;
    refreshToken = response.data.refresh_token;
    tokenExpiry = Date.now() + (response.data.expires_in * 1000);
    
    return accessToken;
  } catch (error) {
    console.error('Token refresh error:', error.response?.data || error.message);
    // Redirect user to re-authenticate
    window.location.href = '/connect';
  }
}

// Function to ensure valid token before making API requests
async function getValidAccessToken() {
  if (Date.now() >= tokenExpiry - 60000) { // Refresh if less than 1 minute remaining
    return await refreshAccessToken();
  }
  return accessToken;
}
```

### 4. Handling Connection Toggle

Allow users to disconnect from your app:

```javascript
async function toggleConnection(enabled = false) {
  try {
    const response = await axios.post('https://your-api-domain.com/oauth/toggle-connection', {
      partnerId: 'your_client_id',
      enabled
    }, {
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    });
    
    return response.data;
  } catch (error) {
    console.error('Error toggling connection:', error.response?.data || error.message);
    throw error;
  }
}
```
