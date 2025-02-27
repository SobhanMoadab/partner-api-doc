## Processing Transactions

Process a transaction and award points to the user:

```
POST /partner-rewards/process-transaction
```

### Authorization

Requires a valid OAuth access token with the `miles:write` scope.

```
Authorization: Bearer {access_token}
```

### Request Body

```json
{
  "userId": "user_id",
  "transactions": [
    {
      "description": "Purchase at Store XYZ",
      "amount": 100.50,
      "currency": "DKK",
      "transactionId": "unique_transaction_id",
      "transactionDate": "2023-07-25T10:30:00Z"
    }
  ]
}
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `userId` | string | ID of the user |
| `transactions` | array | List of transactions |
| `transactions[].description` | string | Description of the transaction |
| `transactions[].amount` | number | Transaction amount |
| `transactions[].currency` | string | Currency code (e.g., "DKK") |
| `transactions[].transactionId` | string | Unique ID for the transaction |
| `transactions[].transactionDate` | string | ISO-8601 timestamp of the transaction |

### Response

#### Success (200 OK)

```json
{
  "message": "Transaction processed successfully"
}
```

#### Error Responses

**400 Bad Request**
```json
{
  "error": "Invalid transaction data"
}
```

**500 Internal Server Error**
```json
{
  "error": "Unexpected error occurred"
}
```

