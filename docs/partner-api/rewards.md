## Awarding Points

Award points to a user based on a partner category:

```
POST /partner-rewards/award-points
```

### Authorization

Requires a valid OAuth access token with the `miles:write` scope.

```
Authorization: Bearer {access_token}
```

### Request Body

```json
{
  "categoryId": "partner_category_id",
  "userId": "user_id",
  "pointsToAward": 100
}
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `categoryId` | string | ID of the partner category |
| `userId` | string | ID of the user to award points to |
| `pointsToAward` | number | Number of points to award |

### Response

#### Success (200 OK)

```json
{
  "message": "Points awarded successfully"
}
```

#### Error Responses

**400 Bad Request**
```json
{
  "error": "Partner category is not active"
}
```

**404 Not Found**
```json
{
  "error": "Partner category not found or does not belong to the partner"
}
```

**500 Internal Server Error**
```json
{
  "error": "Could not award partner points"
}
```

