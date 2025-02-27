# Partner Rewards API

The Partner Rewards API allows you to award points to users based on transactions and partner categories. This API requires OAuth 2.0 authentication with the `miles:write` scope.

## Notes

- Points are awarded only for transactions in DKK currency
- Transactions must have unique transaction IDs
- The partner must have sufficient miles budget to award points
- Keyword matching is performed on transaction descriptions to associate with memberships