# Your API Documentation

Welcome to the official documentation for the Partner API and OAuth integration. This documentation is intended for developers who want to integrate with our platform.

## Overview

This API allows partners to integrate with our platform to:
- Authenticate users via OAuth 2.0
- Award points to users based on transactions
- Process transactions and award points

## Authentication

All API requests must be authenticated using OAuth 2.0. We use the authorization code flow with PKCE (Proof Key for Code Exchange) for security.

### OAuth 2.0 Flow

1. Redirect users to the authorization endpoint
2. Users approve the authorization
3. Exchange the authorization code for access tokens
4. Use the access token to make API requests
5. Refresh the token when it expires

## API Endpoints

The API is organized around RESTful principles. We use standard HTTP response codes and authentication methods.

### OAuth Endpoints

- `GET /oauth/authorize` - Initiate the OAuth flow
- `POST /oauth/consent` - Handle user consent
- `POST /oauth/token` - Exchange authorization code for access token
- `POST /oauth/delete-user-data` - GDPR compliance endpoint
- `POST /oauth/toggle-connection` - Enable/disable a partner connection

### Partner Rewards Endpoints

- `POST /partner-rewards/award-points` - Award points to a user based on partner category
- `POST /partner-rewards/process-transaction` - Process a transaction and award points

