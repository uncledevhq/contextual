# Lenco API Context

## Overview
Lenco API enables developers to build financial services products by providing programmatic access to Lenco accounts, sub-accounts, and transaction histories. The API supports functionalities including:
- Automated disbursement
- Virtual accounts
- Bill payments
- Transaction management
- Account management

## Base URL
```
Production: https://api.lenco.co
Sandbox: https://sandbox-api.lenco.co
```

## Authentication
Lenco API uses API key authentication. Include your API key in the request headers.

### Getting Your API Key

**On the Web:**
1. Log into your Lenco account at https://lenco.co
2. Click on "Lenco Pay" and select "APIs"
3. Click on "Get Started" (modify settings or proceed with defaults)
4. Click on "New API" to generate your API key

**On the App:**
1. Log into your Lenco account
2. Click on "Collections APIs"
3. Click on "New API" to generate your API key

### Authentication Header
```http
Authorization: Bearer YOUR_API_KEY
```
or
```http
X-API-Key: YOUR_API_KEY
```

## Common Endpoints

### Accounts

#### Get Account Information
```http
GET /accounts
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "account_number": "string",
    "account_name": "string",
    "bank_name": "string",
    "balance": "number",
    "currency": "NGN"
  }
}
```

#### Create Virtual Account
```http
POST /accounts/virtual
```

**Request Body:**
```json
{
  "account_name": "string",
  "email": "string",
  "phone": "string",
  "preferred_bank": "string"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "account_number": "string",
    "account_name": "string",
    "bank_name": "string",
    "bank_code": "string"
  }
}
```

### Transactions

#### Get Transactions
```http
GET /transactions
```

**Query Parameters:**
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20)
- `start_date`: Start date (ISO 8601 format)
- `end_date`: End date (ISO 8601 format)
- `status`: Transaction status (pending, success, failed)
- `type`: Transaction type (credit, debit)

**Response:**
```json
{
  "status": "success",
  "data": {
    "transactions": [
      {
        "id": "string",
        "reference": "string",
        "amount": "number",
        "currency": "NGN",
        "type": "credit|debit",
        "status": "success|pending|failed",
        "description": "string",
        "created_at": "ISO 8601 datetime",
        "updated_at": "ISO 8601 datetime"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "total_pages": 5
    }
  }
}
```

#### Get Single Transaction
```http
GET /transactions/{transaction_id}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": "string",
    "reference": "string",
    "amount": "number",
    "currency": "NGN",
    "type": "credit|debit",
    "status": "success|pending|failed",
    "description": "string",
    "metadata": {},
    "created_at": "ISO 8601 datetime",
    "updated_at": "ISO 8601 datetime"
  }
}
```

### Disbursements

#### Create Disbursement
```http
POST /disbursements
```

**Request Body:**
```json
{
  "account_number": "string",
  "account_name": "string",
  "bank_code": "string",
  "amount": "number",
  "currency": "NGN",
  "narration": "string",
  "reference": "string"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": "string",
    "reference": "string",
    "amount": "number",
    "currency": "NGN",
    "status": "pending|processing|success|failed",
    "created_at": "ISO 8601 datetime"
  }
}
```

#### Get Disbursement Status
```http
GET /disbursements/{disbursement_id}
```

### Bill Payments

#### Get Bill Categories
```http
GET /bills/categories
```

**Response:**
```json
{
  "status": "success",
  "data": [
    {
      "id": "string",
      "name": "string",
      "description": "string"
    }
  ]
}
```

#### Get Bill Providers
```http
GET /bills/providers?category={category_id}
```

#### Pay Bill
```http
POST /bills/pay
```

**Request Body:**
```json
{
  "provider_id": "string",
  "customer_id": "string",
  "amount": "number",
  "currency": "NGN",
  "reference": "string"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": "string",
    "reference": "string",
    "amount": "number",
    "status": "success|pending|failed",
    "created_at": "ISO 8601 datetime"
  }
}
```

## Request/Response Format

### Request Headers
```http
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY
Accept: application/json
```

### Response Structure
All responses follow this structure:
```json
{
  "status": "success|error",
  "message": "string",
  "data": {}
}
```

### Error Response
```json
{
  "status": "error",
  "message": "Error description",
  "errors": [
    {
      "field": "field_name",
      "message": "Error message"
    }
  ]
}
```

## HTTP Status Codes
- `200 OK`: Request successful
- `201 Created`: Resource created successfully
- `400 Bad Request`: Invalid request parameters
- `401 Unauthorized`: Invalid or missing API key
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `422 Unprocessable Entity`: Validation error
- `500 Internal Server Error`: Server error

## Rate Limits
- Check the official documentation for current rate limits
- Rate limit headers are typically included in responses:
  - `X-RateLimit-Limit`: Maximum requests per period
  - `X-RateLimit-Remaining`: Remaining requests in current period
  - `X-RateLimit-Reset`: Time when rate limit resets

## Webhooks
Lenco API supports webhooks for real-time event notifications.

### Webhook Events
- `transaction.completed`
- `transaction.failed`
- `disbursement.completed`
- `disbursement.failed`
- `virtual_account.created`
- `bill.payment.completed`

### Webhook Payload
```json
{
  "event": "transaction.completed",
  "data": {
    "id": "string",
    "reference": "string",
    "amount": "number",
    "status": "success"
  },
  "timestamp": "ISO 8601 datetime"
}
```

## Code Examples

### JavaScript/Node.js
```javascript
const axios = require('axios');

const lencoAPI = axios.create({
  baseURL: 'https://api.lenco.co',
  headers: {
    'Authorization': `Bearer ${process.env.LENCO_API_KEY}`,
    'Content-Type': 'application/json'
  }
});

// Get account information
async function getAccount() {
  try {
    const response = await lencoAPI.get('/accounts');
    return response.data;
  } catch (error) {
    console.error('Error:', error.response.data);
    throw error;
  }
}

// Create virtual account
async function createVirtualAccount(accountData) {
  try {
    const response = await lencoAPI.post('/accounts/virtual', accountData);
    return response.data;
  } catch (error) {
    console.error('Error:', error.response.data);
    throw error;
  }
}

// Create disbursement
async function createDisbursement(disbursementData) {
  try {
    const response = await lencoAPI.post('/disbursements', disbursementData);
    return response.data;
  } catch (error) {
    console.error('Error:', error.response.data);
    throw error;
  }
}
```

### Python
```python
import requests

BASE_URL = 'https://api.lenco.co'
API_KEY = 'your_api_key'

headers = {
    'Authorization': f'Bearer {API_KEY}',
    'Content-Type': 'application/json'
}

def get_account():
    response = requests.get(f'{BASE_URL}/accounts', headers=headers)
    return response.json()

def create_virtual_account(account_data):
    response = requests.post(
        f'{BASE_URL}/accounts/virtual',
        json=account_data,
        headers=headers
    )
    return response.json()

def create_disbursement(disbursement_data):
    response = requests.post(
        f'{BASE_URL}/disbursements',
        json=disbursement_data,
        headers=headers
    )
    return response.json()
```

### cURL Examples

#### Get Account
```bash
curl -X GET https://api.lenco.co/accounts \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

#### Create Virtual Account
```bash
curl -X POST https://api.lenco.co/accounts/virtual \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_name": "John Doe",
    "email": "[email protected]",
    "phone": "+2348123456789"
  }'
```

#### Create Disbursement
```bash
curl -X POST https://api.lenco.co/disbursements \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "account_number": "1234567890",
    "account_name": "Jane Doe",
    "bank_code": "058",
    "amount": 10000,
    "currency": "NGN",
    "narration": "Payment for services",
    "reference": "REF123456"
  }'
```

## Best Practices
1. **Store API keys securely**: Never commit API keys to version control. Use environment variables or secure key management systems.
2. **Handle errors gracefully**: Always implement proper error handling for API responses.
3. **Implement retry logic**: For transient failures, implement exponential backoff retry logic.
4. **Validate webhooks**: Verify webhook signatures to ensure requests are from Lenco.
5. **Use idempotency keys**: For critical operations like disbursements, use unique references to prevent duplicate transactions.
6. **Monitor rate limits**: Track rate limit headers and implement appropriate throttling.
7. **Test in sandbox**: Always test integrations in the sandbox environment before going to production.

## Support
- **Documentation**: https://lenco-api.readme.io/
- **Support Email**: [email protected] or [email protected]
- **Help Center**: https://support.lenco.co/en/collections/11610265-api

## Notes
- All monetary amounts are in the smallest currency unit (e.g., kobo for NGN)
- All timestamps are in ISO 8601 format
- The API uses RESTful conventions
- For the most up-to-date endpoint documentation, refer to the official API documentation at https://lenco-api.readme.io/

