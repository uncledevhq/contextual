# Lenco API Integration - Cursor Context

## Project Overview

Integration with Lenco API for payment processing in Zambia supporting Mobile Money and Bank transfers. The API enables developers to build financial services products by providing programmatic access to Lenco accounts, sub-accounts, and transaction histories.

**Key Capabilities:**
- Automated disbursements (transfers)
- Payment collections
- Mobile Money integration (Zambia & Malawi)
- Bank account transfers
- Lenco Money wallet operations
- Transaction management
- Account management

## API Base URLs

- **Production**: `https://api.lenco.co/access/v2`
- **Sandbox**: `https://api.sandbox.lenco.co/access/v2`
- **Payment Widget (Prod)**: `https://pay.lenco.co/js/v1/inline.js`
- **Payment Widget (Sandbox)**: `https://pay.sandbox.lenco.co/js/v1/inline.js`

## Authentication

All API requests require Bearer token authentication:

```http
Authorization: Bearer YOUR_API_TOKEN
```

**Security Notes:**
- Never store API tokens in source control
- API token should be stored as environment variable
- All requests must use HTTPS
- Contact support@lenco.co to get API token or rotate compromised tokens

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

## Response Format

All API responses follow this structure:

```typescript
{
  status: boolean;      // true if successful, false otherwise
  message: string;      // Informational message (log only, don't use for checks)
  data: object | array; // Actual response data
  meta?: {              // Pagination info (when applicable)
    total: number;
    perPage: number;
    currentPage: number;
    pageCount: number;
  }
}
```

## HTTP Status Codes

- **200/201**: Success
- **400**: Validation/client error
- **401**: Unauthorized (invalid/missing API token)
- **404**: Resource not found
- **500-504**: Server error (report immediately)

## Error Codes

| Code | Description |
|------|-------------|
| 01 | Validation error |
| 02 | Insufficient funds |
| 03 | Transfer limit exceeded |
| 04 | Invalid/duplicate reference |
| 05 | Invalid recipient account |
| 06 | Restriction on debit account |
| 07 | Invalid/duplicate bulk transfer reference |
| 08 | Invalid number of objects in bulk transfer |
| 09 | Invalid auth token or authorization denied |
| 10 | General error |
| 11 | Resource not found |
| 12 | Invalid mobile number |
| 13 | Access to resource denied |

## Key Endpoints

### Accounts

- `GET /accounts` - List all accounts
- `GET /accounts/:id` - Get specific account
- `GET /accounts/:id/balance` - Get account balance

**Example Response:**
```json
{
  "status": true,
  "message": "Account retrieved successfully",
  "data": {
    "id": "string",
    "details": {
      "type": "string",
      "accountName": "string",
      "tillNumber": "string"
    },
    "type": "string",
    "status": "string",
    "createdAt": "ISO 8601 datetime",
    "currency": "ZMW",
    "availableBalance": "string",
    "ledgerBalance": "string"
  }
}
```

### Banks & Resolution

- `GET /banks?country=zm` - List banks
- `POST /resolve/bank-account` - Verify bank account
- `POST /resolve/mobile-money` - Verify mobile money account
- `POST /resolve/lenco-money` - Verify Lenco Money wallet
- `POST /resolve/lenco-merchant` - Verify Lenco Merchant till

### Transfer Recipients

- `GET /transfer-recipients` - List recipients
- `GET /transfer-recipients/:id` - Get specific recipient
- `POST /transfer-recipients/bank-account` - Create bank account recipient
- `POST /transfer-recipients/mobile-money` - Create mobile money recipient
- `POST /transfer-recipients/lenco-money` - Create Lenco Money recipient
- `POST /transfer-recipients/lenco-merchant` - Create Lenco Merchant recipient

### Transfers (Sending Money)

- `GET /transfers` - List transfers
- `GET /transfers/:id` - Get transfer by ID
- `GET /transfers/status/:reference` - Get transfer by reference
- `POST /transfers/bank-account` - Transfer to bank
- `POST /transfers/mobile-money` - Transfer to mobile money
- `POST /transfers/lenco-money` - Transfer to Lenco Money
- `POST /transfers/lenco-merchant` - Transfer to Lenco Merchant
- `POST /transfers/account` - Transfer between own accounts

**Example Transfer Request:**
```json
{
  "accountId": "string",
  "amount": 1000,
  "narration": "Payment for services",
  "reference": "unique-ref-123",
  "phone": "0971111111",
  "operator": "airtel"
}
```

**Example Transfer Response:**
```json
{
  "status": true,
  "message": "Transfer initiated",
  "data": {
    "id": "string",
    "amount": "1000",
    "fee": "0",
    "currency": "ZMW",
    "narration": "Payment for services",
    "initiatedAt": "ISO 8601 datetime",
    "completedAt": "ISO 8601 datetime",
    "accountId": "string",
    "creditAccount": {
      "type": "mobile-money",
      "accountName": "John Doe"
    },
    "status": "successful",
    "reasonForFailure": null,
    "reference": "unique-ref-123",
    "lencoReference": "LENCO-REF-456",
    "source": "api"
  }
}
```

### Collections (Receiving Money)

- `GET /collections` - List collections
- `GET /collections/:id` - Get collection by ID
- `GET /collections/status/:reference` - Get collection by reference
- `POST /collections/mobile-money` - Request mobile money payment
- `POST /collections/card` - Request card payment (requires PCI DSS & encryption)

**Example Collection Request:**
```json
{
  "amount": 1000,
  "reference": "unique-ref-123",
  "phone": "0971111111",
  "operator": "airtel",
  "country": "zm",
  "bearer": "customer"
}
```

### Settlements & Transactions

- `GET /settlements` - List settlements
- `GET /settlements/:id` - Get specific settlement
- `GET /transactions` - List transactions
- `GET /transactions/:id` - Get specific transaction

**Query Parameters for Transactions:**
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20)
- `start_date`: Start date (ISO 8601 format)
- `end_date`: End date (ISO 8601 format)
- `status`: Transaction status (pending, successful, failed)
- `type`: Transaction type (credit, debit)

### Encryption

- `GET /encryption-key` - Get RSA public key for payload encryption (required for card collections)

## Payment Collection Widget (Frontend)

```html
<script src="https://pay.lenco.co/js/v1/inline.js"></script>

<script>
LencoPay.getPaid({
  key: 'YOUR_PUBLIC_KEY',
  reference: 'unique-ref-' + Date.now(),
  email: 'customer@email.com',
  amount: 1000,
  currency: "ZMW",
  channels: ["card", "mobile-money"],
  customer: {
    firstName: "John",
    lastName: "Doe",
    phone: "0971111111"
  },
  onSuccess: function(response) {
    // Verify payment on backend
    // response.reference contains the reference
    console.log('Payment successful:', response);
  },
  onClose: function() {
    // User closed widget
    console.log('Widget closed');
  },
  onConfirmationPending: function() {
    // Payment pending confirmation
    console.log('Payment pending confirmation');
  }
});
</script>
```

**Important Widget Notes:**
- Use public key (not secret API token)
- Always verify payment on backend after onSuccess
- Generate unique reference for each transaction
- Amount is NOT in lowest currency unit (use 10.75 not 1075)
- Never call Lenco API directly from frontend (use backend proxy)

## Supported Payment Methods

### Mobile Money Operators (Zambia)

- **airtel** - Airtel Money
- **mtn** - MTN Mobile Money
- **zamtel** - Zamtel Kwacha

### Mobile Money Operators (Malawi)

- **airtel** - Airtel Money
- **tnm** - TNM Mpamba

## Common Data Structures

### Transfer/Collection Status

- `pending` - Transaction initiated but not complete
- `successful` - Transaction completed successfully
- `failed` - Transaction failed
- `pay-offline` - Customer needs to complete on their device (mobile money)
- `3ds-auth-required` - 3D Secure authentication needed (cards)

### Account Types

- `bank-account` - Bank account
- `mobile-money` - Mobile money account
- `lenco-money` - Lenco Money wallet
- `lenco-merchant` - Lenco Merchant till

### Currency

Primary: **ZMW** (Zambian Kwacha)

**Important:** Amounts are NOT in lowest currency unit. Use decimal values (e.g., 10.75 for 10.75 ZMW, not 1075).

## TypeScript Types

```typescript
interface LencoAccount {
  id: string;
  details: {
    type: string;
    accountName: string;
    tillNumber?: string;
  };
  type: string;
  status: string;
  createdAt: string;
  currency: string;
  availableBalance: string | null;
  ledgerBalance: string | null;
}

interface LencoTransfer {
  id: string;
  amount: string;
  fee: string;
  currency: string;
  narration: string;
  initiatedAt: string;
  completedAt: string | null;
  accountId: string;
  creditAccount: {
    type: 'bank-account' | 'mobile-money' | 'lenco-money' | 'lenco-merchant';
    accountName: string;
    // Type-specific fields
  };
  status: 'pending' | 'successful' | 'failed';
  reasonForFailure: string | null;
  reference: string | null;
  lencoReference: string;
  source: 'banking-app' | 'api';
}

interface LencoCollection {
  id: string;
  initiatedAt: string;
  completedAt: string | null;
  amount: string;
  fee: string | null;
  bearer: 'merchant' | 'customer';
  currency: string;
  reference: string | null;
  lencoReference: string;
  type: 'card' | 'mobile-money' | 'bank-account' | null;
  status: 'pending' | 'successful' | 'failed' | 'pay-offline' | '3ds-auth-required';
  source: 'banking-app' | 'api';
  reasonForFailure: string | null;
  settlementStatus: 'pending' | 'settled' | null;
  settlement: Settlement | null;
  mobileMoneyDetails: MobileMoneyDetails | null;
  cardDetails: CardDetails | null;
}

interface TransferRequest {
  accountId: string;
  amount: number;
  narration?: string;
  reference: string;
  // Plus type-specific fields (phone, operator, etc.)
}

interface CollectionRequest {
  amount: number;
  reference: string;
  phone: string;
  operator: 'airtel' | 'mtn' | 'zamtel' | 'tnm';
  country?: 'zm' | 'mw';
  bearer?: 'merchant' | 'customer';
}

interface LencoResponse<T> {
  status: boolean;
  message: string;
  data: T;
  meta?: {
    total: number;
    perPage: number;
    currentPage: number;
    pageCount: number;
  };
}
```

## Code Examples

### JavaScript/Node.js

```javascript
const axios = require('axios');

const lencoAPI = axios.create({
  baseURL: process.env.LENCO_BASE_URL || 'https://api.lenco.co/access/v2',
  headers: {
    'Authorization': `Bearer ${process.env.LENCO_API_TOKEN}`,
    'Content-Type': 'application/json'
  }
});

// Get account information
async function getAccount(accountId) {
  try {
    const response = await lencoAPI.get(`/accounts/${accountId}`);
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}

// Get account balance
async function getAccountBalance(accountId) {
  try {
    const response = await lencoAPI.get(`/accounts/${accountId}/balance`);
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}

// Create mobile money transfer
async function transferToMobileMoney(transferData) {
  try {
    const response = await lencoAPI.post('/transfers/mobile-money', transferData);
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}

// Create collection request
async function requestCollection(collectionData) {
  try {
    const response = await lencoAPI.post('/collections/mobile-money', collectionData);
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}

// Get transfer status by reference
async function getTransferStatus(reference) {
  try {
    const response = await lencoAPI.get(`/transfers/status/${reference}`);
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}

// Resolve bank account
async function resolveBankAccount(accountNumber, bankCode) {
  try {
    const response = await lencoAPI.post('/resolve/bank-account', {
      accountNumber,
      bankCode
    });
    if (response.data.status) {
      return response.data.data;
    }
    throw new Error(response.data.message);
  } catch (error) {
    console.error('Error:', error.response?.data || error.message);
    throw error;
  }
}
```

### Python

```python
import requests
import os

BASE_URL = os.getenv('LENCO_BASE_URL', 'https://api.lenco.co/access/v2')
API_TOKEN = os.getenv('LENCO_API_TOKEN')

headers = {
    'Authorization': f'Bearer {API_TOKEN}',
    'Content-Type': 'application/json'
}

def get_account(account_id):
    response = requests.get(f'{BASE_URL}/accounts/{account_id}', headers=headers)
    data = response.json()
    if data.get('status'):
        return data.get('data')
    raise Exception(data.get('message', 'Unknown error'))

def get_account_balance(account_id):
    response = requests.get(f'{BASE_URL}/accounts/{account_id}/balance', headers=headers)
    data = response.json()
    if data.get('status'):
        return data.get('data')
    raise Exception(data.get('message', 'Unknown error'))

def transfer_to_mobile_money(transfer_data):
    response = requests.post(
        f'{BASE_URL}/transfers/mobile-money',
        json=transfer_data,
        headers=headers
    )
    data = response.json()
    if data.get('status'):
        return data.get('data')
    raise Exception(data.get('message', 'Unknown error'))

def request_collection(collection_data):
    response = requests.post(
        f'{BASE_URL}/collections/mobile-money',
        json=collection_data,
        headers=headers
    )
    data = response.json()
    if data.get('status'):
        return data.get('data')
    raise Exception(data.get('message', 'Unknown error'))

def get_transfer_status(reference):
    response = requests.get(
        f'{BASE_URL}/transfers/status/{reference}',
        headers=headers
    )
    data = response.json()
    if data.get('status'):
        return data.get('data')
    raise Exception(data.get('message', 'Unknown error'))
```

### cURL Examples

#### Get Account
```bash
curl -X GET https://api.lenco.co/access/v2/accounts/{account_id} \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

#### Get Account Balance
```bash
curl -X GET https://api.lenco.co/access/v2/accounts/{account_id}/balance \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

#### Transfer to Mobile Money
```bash
curl -X POST https://api.lenco.co/access/v2/transfers/mobile-money \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "account_id",
    "amount": 1000,
    "narration": "Payment for services",
    "reference": "unique-ref-123",
    "phone": "0971111111",
    "operator": "airtel",
    "country": "zm"
  }'
```

#### Request Collection
```bash
curl -X POST https://api.lenco.co/access/v2/collections/mobile-money \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 1000,
    "reference": "unique-ref-123",
    "phone": "0971111111",
    "operator": "airtel",
    "country": "zm",
    "bearer": "customer"
  }'
```

#### Get Transfer Status
```bash
curl -X GET https://api.lenco.co/access/v2/transfers/status/unique-ref-123 \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

## Webhooks

Setup webhook URL with support@lenco.co to receive:
- `transfer.successful`
- `transfer.failed`
- `collection.successful`
- `collection.failed`
- `collection.settled`
- `transaction.credit`
- `transaction.debit`

### Webhook Verification

```javascript
const crypto = require('crypto');

function verifyWebhookSignature(req, apiToken) {
  const webhookHashKey = crypto.createHash("sha256")
    .update(apiToken)
    .digest("hex");

  const signature = crypto.createHmac('sha512', webhookHashKey)
    .update(JSON.stringify(req.body))
    .digest('hex');

  const receivedSignature = req.headers['x-lenco-signature'];
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(receivedSignature)
  );
}

// Express.js example
app.post('/webhooks/lenco', express.json(), (req, res) => {
  if (!verifyWebhookSignature(req, process.env.LENCO_API_TOKEN)) {
    return res.status(401).send('Invalid signature');
  }

  const event = req.body.event;
  const data = req.body.data;

  // Process webhook event
  switch (event) {
    case 'transfer.successful':
      // Handle successful transfer
      break;
    case 'collection.successful':
      // Handle successful collection
      break;
    // ... other events
  }

  // Always respond quickly
  res.status(200).send('OK');
});
```

**Webhook Response:**
- Respond with 200/201/202 to acknowledge
- Other status codes = unacknowledged (retry every 30 min for 24 hrs)
- Don't perform long-running tasks before responding

## Test Data

### Mobile Money Test Accounts (Sandbox)

| Phone | Operator | Result | Error |
|-------|----------|--------|-------|
| 0961111111 | mtn | Successful | - |
| 0962222222 | mtn | Failed | Not enough funds |
| 0971111111 | airtel (zm) | Successful | - |
| 0972222222 | airtel (zm) | Failed | Incorrect Pin |
| 0881111111 | tnm | Successful | - |
| 0991111111 | airtel (mw) | Successful | - |

### Test Cards (Sandbox)

| Type | Number | CVV | Expiry |
|------|--------|-----|--------|
| Visa | 4622 9431 2701 3705 | 838 | Any future date |
| Visa | 4622 9431 2701 3747 | 370 | Any future date |
| Mastercard | 5555 5555 5555 4444 | Any 3 digits | Any future date |

## Environment Variables

```bash
# Required
LENCO_API_TOKEN=your_secret_api_token
LENCO_PUBLIC_KEY=your_public_key
LENCO_WEBHOOK_URL=https://your-domain.com/webhooks/lenco

# Optional
LENCO_ENVIRONMENT=sandbox # or production
LENCO_BASE_URL=https://api.lenco.co/access/v2
```

## Best Practices

### References
- Generate unique references for each transaction
- Use format: `prefix-timestamp` or `prefix-uuid`
- Only alphanumeric, `-`, `.`, `_` allowed
- Store references in your database

### Error Handling
- Check HTTP status code first
- Then check `response.status` boolean
- Use `errorCode` for specific error handling
- Log `message` for debugging
- Implement retry logic with exponential backoff for transient failures

### Verification Flow
1. User completes payment (widget or API)
2. Your frontend receives success callback
3. Frontend calls YOUR backend endpoint
4. Backend verifies with Lenco API using reference
5. Backend updates database and responds to frontend

### Re-query Pattern
Don't rely solely on webhooks. Implement polling:
- Query `/transfers/status/:reference` every 30 minutes
- Continue until status is `successful` or `failed`
- Handles cases where webhooks fail due to downtime

### Security
- Store API tokens securely: Never commit API keys to version control. Use environment variables or secure key management systems.
- Never call Lenco API directly from frontend (use backend proxy)
- Always verify webhook signatures
- Use HTTPS for all requests

### Card Collections Special Notes
- Requires PCI DSS compliance
- Payload must be encrypted using JWE
- Use RSA-OAEP-256 with A256GCM encryption
- Get encryption key from `/encryption-key` endpoint
- Don't cache/reuse encryption keys
- Handle 3DS authentication redirects
- Never log card details

## Common Pitfalls to Avoid

1. ❌ Don't use localStorage/sessionStorage in frontend for API tokens
2. ❌ Don't call Lenco API directly from frontend (use backend proxy)
3. ❌ Don't reuse references across transactions
4. ❌ Don't convert amounts to lowest currency unit (kobo/cents) - use decimal values
5. ❌ Don't rely solely on webhooks (implement re-query)
6. ❌ Don't store API tokens in version control
7. ❌ Don't forget to verify webhook signatures
8. ❌ Don't perform long tasks before responding to webhooks
9. ❌ Don't use `message` field for business logic checks (use `status` boolean)
10. ❌ Don't cache encryption keys for card collections

## Support

- **Email**: support@lenco.co
- **Documentation**: https://docs.lenco.co
- **API Documentation**: https://lenco-api.readme.io/
- **Help Center**: https://support.lenco.co/en/collections/11610265-api

## Notes

- All timestamps are in ISO 8601 format
- The API uses RESTful conventions
- Amounts are in decimal format (NOT lowest currency unit)
- Primary currency: ZMW (Zambian Kwacha)
- For the most up-to-date endpoint documentation, refer to the official API documentation at https://docs.lenco.co
