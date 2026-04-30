---
title: Error codes
deprecated: false
hidden: false
metadata:
  robots: index
---
## Error Codes

| Code | Meaning                                                                                   |
| :--- | :---------------------------------------------------------------------------------------- |
| 400  | Bad Request - Your request is invalid.                                                    |
| 401  | Unauthorized - Your API key is wrong.                                                     |
| 403  | Forbidden - Your role doesn't have enough permissions to perform this action.             |
| 404  | Not Found - The resource could not be found.                                              |
| 405  | Method Not Allowed - You tried to access a resource with an invalid method.               |
| 410  | Gone - The resource requested has been removed from our servers.                          |
| 413  | Payload Too Large - Your bulk request is too large. Please see the limitations.           |
| 422  | Unprocessable Entity - You have validation errors in some fields.                         |
| 429  | Too Many Requests - You're performing too many requests! Slow down!                       |
| 500  | Internal Server Error - We had a problem with our server. Try again later.                |
| 503  | Service Unavailable - We're temporarily offline for maintenance. Please try again later.  |
| 507  | Insufficient Storage - Storage is full. Please, remove something or upgrade your account. |
