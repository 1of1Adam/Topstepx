---
title: "TopStepX API Complete Reference for AI Models"
api_version: "1.0.0"
host: "api.topstepx.com"
schemes: ["https"]
base_url: "https://api.topstepx.com"
documentation_type: "ai_optimized"
total_endpoints: 18
api_modules:
  - Account
  - Auth
  - Contract
  - History
  - Order
  - Position
  - Status
  - Trade
authentication:
  - api_key: "Header or Query Parameter"
  - session_token: "Bearer Token"
realtime_support:
  market_hub: "https://rtc.topstepx.com/hubs/market"
  user_hub: "https://rtc.topstepx.com/hubs/user"
last_updated: "2025-07-06T19:49:48.808Z"
---

# TopStepX API Complete Reference

## API Information
- **Base URL**: https://api.topstepx.com
- **Protocol**: HTTPS
- **API Version**: 1.0.0
- **Total Endpoints**: 18

## Quick Access by Functionality

### Account APIs
- `POST /api/Account/search` - Searches for accounts based on the provided request.

### Auth APIs
- `POST /api/Auth/loginApp` - Login as the specified user using the specified application.
- `POST /api/Auth/loginKey` - Login as the specified user using the specified API key.
- `POST /api/Auth/logout` - Logs out the current authenticated user.
- `POST /api/Auth/validate` - Validates the current user's session.

### Contract APIs
- `POST /api/Contract/search` - Searches for contracts based on the provided search criteria.
- `POST /api/Contract/searchById` - Searches for a contract by its ID.

### History APIs
- `POST /api/History/retrieveBars` - Retrieves historical bars based on the specified request parameters.

### Order APIs
- `POST /api/Order/search` - Searches for orders based on the provided request.
- `POST /api/Order/searchOpen` - Searches for open (working/active) orders based on the provided request.
- `POST /api/Order/place` - Places a new order based on the provided request.
- `POST /api/Order/cancel` - Cancels an existing order based on the provided request.
- `POST /api/Order/modify` - Modifies an existing order based on the provided request.

### Position APIs
- `POST /api/Position/searchOpen` - Searches for open positions based on the provided request.
- `POST /api/Position/closeContract` - Closes a contract position based on the provided request.
- `POST /api/Position/partialCloseContract` - Partially closes a contract position based on the provided request.

### Status APIs
- `GET /api/Status/ping` - Handles the ping request to check the status of the API.

### Trade APIs
- `POST /api/Trade/search` - Searches for half-turn trades based on the provided request parameters.

## Account Module

**Total Endpoints**: 1

### POST /api/Account/search
**Operation ID**: Account_SearchAccounts
**Summary**: Searches for accounts based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Account/search`
**Parameters**:
- **request** (body): object - The request containing search criteria.
  - *Required*
  - *Type Reference*: #/definitions/SearchAccountRequest
**Responses**:
- **200**: The search account response.
  - *Response Type*: #/definitions/SearchAccountResponse
**Examples**:
/api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Auth Module

**Total Endpoints**: 4

### POST /api/Auth/loginApp
**Operation ID**: Auth_LoginApp
**Summary**: Login as the specified user using the specified application.
**Full URL**: `https://api.topstepx.com/api/Auth/loginApp`
**Parameters**:
- **request** (body): object - The login request.
  - *Required*
  - *Type Reference*: #/definitions/LoginAppRequest
**Responses**:
- **200**: 
  - *Response Type*: #/definitions/LoginResponse
**Examples**:
/api/Auth/loginApp
API Reference: /api/Auth/loginApp
```

----------------------------------------

TITLE: cURL Example for API Key Authentication
DESCRIPTION: Demonstrates how to send a POST request to the ProjectX Gateway API's /api/Auth/loginKey endpoint using cURL. The request body is a JSON payload containing `userName` and `apiKey`.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-api-key

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/loginKey' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "adampeng",
    "apiKey": "RZg5ih1GbpjnaT41yI3TdmgJU2/vGydjU/9G2IvRX/w="
  }'
```

----------------------------------------

TITLE: Example API Response for Successful Authentication
DESCRIPTION: This JSON object represents a successful response from the ProjectX Gateway API's authentication endpoint. It contains the `token` (session token), a `success` boolean, an `errorCode`, and an `errorMessage` (which is null on success).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-as-application

LANGUAGE: json
CODE:
```
{
    "token": "your_session_token_here",
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: JSON Response for Successful Authentication
DESCRIPTION: An example of the JSON response received upon successful authentication from the /api/Auth/loginKey endpoint. It includes the `token` (session token), `success` status, `errorCode`, and `errorMessage`.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-api-key

LANGUAGE: JSON
CODE:
```
{
    "token": "your_session_token_here",
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Authenticate Application with ProjectX Gateway API using cURL
DESCRIPTION: This cURL command demonstrates how to send a POST request to the ProjectX Gateway API's `loginApp` endpoint. It includes necessary headers (`accept`, `Content-Type`) and a JSON request body containing `userName`, `password`, `deviceId`, `appId`, and `verifyKey` for authentication.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-as-application

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/loginApp' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "yourUsername",
    "password": "yourPassword",
    "deviceId": "yourDeviceId",
    "appId": "B76015F2-04D3-477E-9191-C5E22CB2C957",
    "verifyKey": "yourVerifyKey"
  }'
```

----------------------------------------

TITLE: Place an Order API Reference
DESCRIPTION: Detailed API documentation for the 'Place an Order' endpoint, including its URL, purpose, and a comprehensive list of all supported parameters with their types, descriptions, and optionality.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/place
Description: Place an order.

Parameters:
  accountId: integer (Required, Not Nullable) - The account ID.
  contractId: string (Required, Not Nullable) - The contract ID.
  type: integer (Required, Not Nullable) - The order type:
    1 = Limit
    2 = Market
    4 = Stop
    5 = TrailingStop
    6 = JoinBid
    7 = JoinAsk
  side: integer (Required, Not Nullable) - The side of the order:
    0 = Bid (buy)
    1 = Ask (sell)
  size: integer (Required, Not Nullable) - The size of the order.
  limitPrice: decimal (Optional, Nullable) - The limit price for the order, if applicable.
  stopPrice: decimal (Optional, Nullable) - The stop price for the order, if applicable.
  trailPrice: decimal (Optional, Nullable) - The trail price for the order, if applicable.
  customTag: string (Optional, Nullable) - An optional custom tag for the order.
  linkedOrderId: integer (Optional, Nullable) - The linked order id.
```

----------------------------------------

TITLE: cURL Example Request for Placing an Order
DESCRIPTION: An example cURL command demonstrating how to send a POST request to the 'Place an Order' API endpoint with a JSON payload containing typical order parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/place' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "contractId": "CON.F.US.DA6.M25",
    "type": 2,
    "side": 1,
    "size": 1,
    "limitPrice": null,
    "stopPrice": null,
    "trailPrice": null,
    "customTag": null,
    "linkedOrderId": null
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Order Placement
DESCRIPTION: An example JSON response received upon successful placement of an order, including the generated order ID, a success flag, and error details if any.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: json
CODE:
```
{
    "orderId": 9056,
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Orders Endpoint
DESCRIPTION: Comprehensive documentation for the 'Search for Orders' API endpoint, detailing its URL, HTTP method, request parameters, and their types and requirements. This includes information on account filtering and timestamp-based searches, along with a link to the interactive Swagger documentation.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/search
API Reference: https://api.topstepx.com/swagger/index.html#/Order/Order_SearchOrders

Description: Search for orders.

Parameters:
- accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
- startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
- endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true
```

----------------------------------------

TITLE: Example cURL Request for Order Search API
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's Order search endpoint using cURL. This example includes the required JSON body with 'accountId', 'startTimestamp', and 'endTimestamp' parameters for filtering orders within a specified time range.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 202,
    "startTimestamp": "2024-12-30T16:48:16.003Z",
    "endTimestamp": "2025-12-30T16:48:16.003Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response from Order Search API
DESCRIPTION: Illustrates a successful JSON response from the ProjectX Gateway API's Order search endpoint. This example showcases the structure of returned order objects, including properties like 'id', 'accountId', 'contractId', 'creationTimestamp', 'status', 'type', 'side', 'size', and price details, along with the overall 'success' status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: json
CODE:
```
{
    "orders": [
        {
            "id": 26060,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:10.142532+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 0,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        },
        {
            "id": 26062,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:53.043234+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference for Partially Close Position Endpoint
DESCRIPTION: Documents the API endpoint for partially closing a position, including its URL, HTTP method, and required parameters. This endpoint allows users to reduce the size of an existing open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Position/partialCloseContract
Description: Partially close a position.
Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
  - Name: contractId
    Type: string
    Description: The contract ID.
    Required: true
  - Name: size
    Type: integer
    Description: The size to close.
    Required: true
```

----------------------------------------

TITLE: Example cURL Request to Partially Close Position
DESCRIPTION: Demonstrates how to send a POST request using cURL to partially close a position. The request includes necessary headers and a JSON body specifying the account ID, contract ID, and the size to close.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/partialCloseContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25",
    "size": 1
  }'
```

----------------------------------------

TITLE: API Endpoint Definition for Close Positions
DESCRIPTION: Defines the HTTP method, URL, and required parameters for the 'Close Positions' API endpoint, which allows users to close an open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: APIDOC
CODE:
```
POST https://api.topstepx.com/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Auth/loginKey
**Operation ID**: Auth_LoginKey
**Summary**: Login as the specified user using the specified API key.
**Full URL**: `https://api.topstepx.com/api/Auth/loginKey`
**Parameters**:
- **request** (body): object - The login request.
  - *Required*
  - *Type Reference*: #/definitions/LoginApiKeyRequest
**Responses**:
- **200**: 
  - *Response Type*: #/definitions/LoginResponse
**Examples**:
/api/Auth/loginKey
URL: https://api.topstepx.com/api/Auth/loginKey
Request Body:
  userName: string
  apiKey: string
Response Body:
  token: string
  success: boolean
  errorCode: integer
  errorMessage: string | null
Reference: https://api.topstepx.com/swagger/index.html#/Auth/Auth_LoginKey
```

----------------------------------------

TITLE: API Reference: Authenticate Application Login
DESCRIPTION: Documents the `loginApp` endpoint for authenticating authorized applications with the ProjectX Gateway API. It specifies the POST method, the API URL, and provides a direct link to the Swagger documentation for detailed reference.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-as-application

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/loginApp
API Reference: /api/Auth/loginApp
```

----------------------------------------

TITLE: cURL Example for API Key Authentication
DESCRIPTION: Demonstrates how to send a POST request to the ProjectX Gateway API's /api/Auth/loginKey endpoint using cURL. The request body is a JSON payload containing `userName` and `apiKey`.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-api-key

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/loginKey' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "adampeng",
    "apiKey": "RZg5ih1GbpjnaT41yI3TdmgJU2/vGydjU/9G2IvRX/w="
  }'
```

----------------------------------------

TITLE: Example API Response for Successful Authentication
DESCRIPTION: This JSON object represents a successful response from the ProjectX Gateway API's authentication endpoint. It contains the `token` (session token), a `success` boolean, an `errorCode`, and an `errorMessage` (which is null on success).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-as-application

LANGUAGE: json
CODE:
```
{
    "token": "your_session_token_here",
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: JSON Response for Successful Authentication
DESCRIPTION: An example of the JSON response received upon successful authentication from the /api/Auth/loginKey endpoint. It includes the `token` (session token), `success` status, `errorCode`, and `errorMessage`.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-api-key

LANGUAGE: JSON
CODE:
```
{
    "token": "your_session_token_here",
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Authenticate Application with ProjectX Gateway API using cURL
DESCRIPTION: This cURL command demonstrates how to send a POST request to the ProjectX Gateway API's `loginApp` endpoint. It includes necessary headers (`accept`, `Content-Type`) and a JSON request body containing `userName`, `password`, `deviceId`, `appId`, and `verifyKey` for authentication.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/authenticate/authenticate-as-application

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/loginApp' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "userName": "yourUsername",
    "password": "yourPassword",
    "deviceId": "yourDeviceId",
    "appId": "B76015F2-04D3-477E-9191-C5E22CB2C957",
    "verifyKey": "yourVerifyKey"
  }'
```

----------------------------------------

TITLE: Place an Order API Reference
DESCRIPTION: Detailed API documentation for the 'Place an Order' endpoint, including its URL, purpose, and a comprehensive list of all supported parameters with their types, descriptions, and optionality.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/place
Description: Place an order.

Parameters:
  accountId: integer (Required, Not Nullable) - The account ID.
  contractId: string (Required, Not Nullable) - The contract ID.
  type: integer (Required, Not Nullable) - The order type:
    1 = Limit
    2 = Market
    4 = Stop
    5 = TrailingStop
    6 = JoinBid
    7 = JoinAsk
  side: integer (Required, Not Nullable) - The side of the order:
    0 = Bid (buy)
    1 = Ask (sell)
  size: integer (Required, Not Nullable) - The size of the order.
  limitPrice: decimal (Optional, Nullable) - The limit price for the order, if applicable.
  stopPrice: decimal (Optional, Nullable) - The stop price for the order, if applicable.
  trailPrice: decimal (Optional, Nullable) - The trail price for the order, if applicable.
  customTag: string (Optional, Nullable) - An optional custom tag for the order.
  linkedOrderId: integer (Optional, Nullable) - The linked order id.
```

----------------------------------------

TITLE: cURL Example Request for Placing an Order
DESCRIPTION: An example cURL command demonstrating how to send a POST request to the 'Place an Order' API endpoint with a JSON payload containing typical order parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/place' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "contractId": "CON.F.US.DA6.M25",
    "type": 2,
    "side": 1,
    "size": 1,
    "limitPrice": null,
    "stopPrice": null,
    "trailPrice": null,
    "customTag": null,
    "linkedOrderId": null
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Order Placement
DESCRIPTION: An example JSON response received upon successful placement of an order, including the generated order ID, a success flag, and error details if any.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: json
CODE:
```
{
    "orderId": 9056,
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Orders Endpoint
DESCRIPTION: Comprehensive documentation for the 'Search for Orders' API endpoint, detailing its URL, HTTP method, request parameters, and their types and requirements. This includes information on account filtering and timestamp-based searches, along with a link to the interactive Swagger documentation.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/search
API Reference: https://api.topstepx.com/swagger/index.html#/Order/Order_SearchOrders

Description: Search for orders.

Parameters:
- accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
- startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
- endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true
```

----------------------------------------

TITLE: Example cURL Request for Order Search API
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's Order search endpoint using cURL. This example includes the required JSON body with 'accountId', 'startTimestamp', and 'endTimestamp' parameters for filtering orders within a specified time range.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 202,
    "startTimestamp": "2024-12-30T16:48:16.003Z",
    "endTimestamp": "2025-12-30T16:48:16.003Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response from Order Search API
DESCRIPTION: Illustrates a successful JSON response from the ProjectX Gateway API's Order search endpoint. This example showcases the structure of returned order objects, including properties like 'id', 'accountId', 'contractId', 'creationTimestamp', 'status', 'type', 'side', 'size', and price details, along with the overall 'success' status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: json
CODE:
```
{
    "orders": [
        {
            "id": 26060,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:10.142532+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 0,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        },
        {
            "id": 26062,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:53.043234+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference for Partially Close Position Endpoint
DESCRIPTION: Documents the API endpoint for partially closing a position, including its URL, HTTP method, and required parameters. This endpoint allows users to reduce the size of an existing open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Position/partialCloseContract
Description: Partially close a position.
Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
  - Name: contractId
    Type: string
    Description: The contract ID.
    Required: true
  - Name: size
    Type: integer
    Description: The size to close.
    Required: true
```

----------------------------------------

TITLE: Example cURL Request to Partially Close Position
DESCRIPTION: Demonstrates how to send a POST request using cURL to partially close a position. The request includes necessary headers and a JSON body specifying the account ID, contract ID, and the size to close.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/partialCloseContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25",
    "size": 1
  }'
```

----------------------------------------

TITLE: API Endpoint Definition for Close Positions
DESCRIPTION: Defines the HTTP method, URL, and required parameters for the 'Close Positions' API endpoint, which allows users to close an open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: APIDOC
CODE:
```
POST https://api.topstepx.com/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Auth/logout
**Operation ID**: Auth_Logout
**Summary**: Logs out the current authenticated user.
**Full URL**: `https://api.topstepx.com/api/Auth/logout`
**Responses**:
- **200**: 
  - *Response Type*: #/definitions/LogoutResponse

### POST /api/Auth/validate
**Operation ID**: Auth_Validate
**Summary**: Validates the current user's session.
**Full URL**: `https://api.topstepx.com/api/Auth/validate`
**Responses**:
- **200**: 
  - *Response Type*: #/definitions/ValidateResponse
**Examples**:
/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Contract Module

**Total Endpoints**: 2

### POST /api/Contract/search
**Operation ID**: Contract_SearchContracts
**Summary**: Searches for contracts based on the provided search criteria.
**Full URL**: `https://api.topstepx.com/api/Contract/search`
**Parameters**:
- **request** (body): object - The search criteria for finding contracts.
  - *Required*
  - *Type Reference*: #/definitions/SearchContractRequest
**Responses**:
- **200**: A response containing the search results, including any matching contracts.
  - *Response Type*: #/definitions/SearchContractResponse
**Examples**:
/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Contract/searchById
**Operation ID**: Contract_SearchContractById
**Summary**: Searches for a contract by its ID.
**Full URL**: `https://api.topstepx.com/api/Contract/searchById`
**Parameters**:
- **request** (body): object - The request containing the contract ID.
  - *Required*
  - *Type Reference*: #/definitions/SearchContractByIdRequest
**Responses**:
- **200**: A response containing the search result, including the matching contract if found.
  - *Response Type*: #/definitions/SearchContractByIdResponse
**Examples**:
/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## History Module

**Total Endpoints**: 1

### POST /api/History/retrieveBars
**Operation ID**: History_GetBars
**Summary**: Retrieves historical bars based on the specified request parameters.
**Full URL**: `https://api.topstepx.com/api/History/retrieveBars`
**Parameters**:
- **request** (body): object - The request containing parameters for retrieving historical bars.
  - *Required*
  - *Type Reference*: #/definitions/RetrieveBarRequest
**Responses**:
- **200**: The response with the retrieved bars.
  - *Response Type*: #/definitions/RetrieveBarResponse
**Examples**:
/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Order Module

**Total Endpoints**: 5

### POST /api/Order/search
**Operation ID**: Order_SearchOrders
**Summary**: Searches for orders based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Order/search`
**Parameters**:
- **request** (body): object - A request containing search criteria.
  - *Required*
  - *Type Reference*: #/definitions/SearchOrderRequest
**Responses**:
- **200**: A response with search results.
  - *Response Type*: #/definitions/SearchOrderResponse
**Examples**:
/api/Order/search
API Reference: https://api.topstepx.com/swagger/index.html#/Order/Order_SearchOrders

Description: Search for orders.

Parameters:
- accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
- startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
- endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true
```

----------------------------------------

TITLE: Example cURL Request for Order Search API
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's Order search endpoint using cURL. This example includes the required JSON body with 'accountId', 'startTimestamp', and 'endTimestamp' parameters for filtering orders within a specified time range.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 202,
    "startTimestamp": "2024-12-30T16:48:16.003Z",
    "endTimestamp": "2025-12-30T16:48:16.003Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response from Order Search API
DESCRIPTION: Illustrates a successful JSON response from the ProjectX Gateway API's Order search endpoint. This example showcases the structure of returned order objects, including properties like 'id', 'accountId', 'contractId', 'creationTimestamp', 'status', 'type', 'side', 'size', and price details, along with the overall 'success' status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: json
CODE:
```
{
    "orders": [
        {
            "id": 26060,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:10.142532+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 0,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        },
        {
            "id": 26062,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:53.043234+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference for Partially Close Position Endpoint
DESCRIPTION: Documents the API endpoint for partially closing a position, including its URL, HTTP method, and required parameters. This endpoint allows users to reduce the size of an existing open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Position/partialCloseContract
Description: Partially close a position.
Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
  - Name: contractId
    Type: string
    Description: The contract ID.
    Required: true
  - Name: size
    Type: integer
    Description: The size to close.
    Required: true
```

----------------------------------------

TITLE: Example cURL Request to Partially Close Position
DESCRIPTION: Demonstrates how to send a POST request using cURL to partially close a position. The request includes necessary headers and a JSON body specifying the account ID, contract ID, and the size to close.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/partialCloseContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25",
    "size": 1
  }'
```

----------------------------------------

TITLE: API Endpoint Definition for Close Positions
DESCRIPTION: Defines the HTTP method, URL, and required parameters for the 'Close Positions' API endpoint, which allows users to close an open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: APIDOC
CODE:
```
POST https://api.topstepx.com/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Order/searchOpen
**Operation ID**: Order_SearchOpenOrders
**Summary**: Searches for open (working/active) orders based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Order/searchOpen`
**Parameters**:
- **request** (body): object - A request containing search criteria.
  - *Required*
  - *Type Reference*: #/definitions/SearchOpenOrderRequest
**Responses**:
- **200**: A response with search results.
  - *Response Type*: #/definitions/SearchOrderResponse
**Examples**:
/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Order/place
**Operation ID**: Order_PlaceOrder
**Summary**: Places a new order based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Order/place`
**Parameters**:
- **request** (body): object - A request containing order details.
  - *Required*
  - *Type Reference*: #/definitions/PlaceOrderRequest
**Responses**:
- **200**: A response with order placement details.
  - *Response Type*: #/definitions/PlaceOrderResponse
**Examples**:
/api/Order/place
Description: Place an order.

Parameters:
  accountId: integer (Required, Not Nullable) - The account ID.
  contractId: string (Required, Not Nullable) - The contract ID.
  type: integer (Required, Not Nullable) - The order type:
    1 = Limit
    2 = Market
    4 = Stop
    5 = TrailingStop
    6 = JoinBid
    7 = JoinAsk
  side: integer (Required, Not Nullable) - The side of the order:
    0 = Bid (buy)
    1 = Ask (sell)
  size: integer (Required, Not Nullable) - The size of the order.
  limitPrice: decimal (Optional, Nullable) - The limit price for the order, if applicable.
  stopPrice: decimal (Optional, Nullable) - The stop price for the order, if applicable.
  trailPrice: decimal (Optional, Nullable) - The trail price for the order, if applicable.
  customTag: string (Optional, Nullable) - An optional custom tag for the order.
  linkedOrderId: integer (Optional, Nullable) - The linked order id.
```

----------------------------------------

TITLE: cURL Example Request for Placing an Order
DESCRIPTION: An example cURL command demonstrating how to send a POST request to the 'Place an Order' API endpoint with a JSON payload containing typical order parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/place' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "contractId": "CON.F.US.DA6.M25",
    "type": 2,
    "side": 1,
    "size": 1,
    "limitPrice": null,
    "stopPrice": null,
    "trailPrice": null,
    "customTag": null,
    "linkedOrderId": null
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Order Placement
DESCRIPTION: An example JSON response received upon successful placement of an order, including the generated order ID, a success flag, and error details if any.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: json
CODE:
```
{
    "orderId": 9056,
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Orders Endpoint
DESCRIPTION: Comprehensive documentation for the 'Search for Orders' API endpoint, detailing its URL, HTTP method, request parameters, and their types and requirements. This includes information on account filtering and timestamp-based searches, along with a link to the interactive Swagger documentation.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/search
API Reference: https://api.topstepx.com/swagger/index.html#/Order/Order_SearchOrders

Description: Search for orders.

Parameters:
- accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
- startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
- endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true
```

----------------------------------------

TITLE: Example cURL Request for Order Search API
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's Order search endpoint using cURL. This example includes the required JSON body with 'accountId', 'startTimestamp', and 'endTimestamp' parameters for filtering orders within a specified time range.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 202,
    "startTimestamp": "2024-12-30T16:48:16.003Z",
    "endTimestamp": "2025-12-30T16:48:16.003Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response from Order Search API
DESCRIPTION: Illustrates a successful JSON response from the ProjectX Gateway API's Order search endpoint. This example showcases the structure of returned order objects, including properties like 'id', 'accountId', 'contractId', 'creationTimestamp', 'status', 'type', 'side', 'size', and price details, along with the overall 'success' status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: json
CODE:
```
{
    "orders": [
        {
            "id": 26060,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:10.142532+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 0,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        },
        {
            "id": 26062,
            "accountId": 545,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-14T17:49:53.043234+00:00",
            "updateTimestamp": null,
            "status": 2,
            "type": 2,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": null
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference for Partially Close Position Endpoint
DESCRIPTION: Documents the API endpoint for partially closing a position, including its URL, HTTP method, and required parameters. This endpoint allows users to reduce the size of an existing open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Position/partialCloseContract
Description: Partially close a position.
Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
  - Name: contractId
    Type: string
    Description: The contract ID.
    Required: true
  - Name: size
    Type: integer
    Description: The size to close.
    Required: true
```

----------------------------------------

TITLE: Example cURL Request to Partially Close Position
DESCRIPTION: Demonstrates how to send a POST request using cURL to partially close a position. The request includes necessary headers and a JSON body specifying the account ID, contract ID, and the size to close.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/partialCloseContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25",
    "size": 1
  }'
```

----------------------------------------

TITLE: API Endpoint Definition for Close Positions
DESCRIPTION: Defines the HTTP method, URL, and required parameters for the 'Close Positions' API endpoint, which allows users to close an open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: APIDOC
CODE:
```
POST https://api.topstepx.com/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Order/cancel
**Operation ID**: Order_CancelOrder
**Summary**: Cancels an existing order based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Order/cancel`
**Parameters**:
- **request** (body): object - A request containing order cancellation details.
  - *Required*
  - *Type Reference*: #/definitions/CancelOrderRequest
**Responses**:
- **200**: A response with order cancellation details.
  - *Response Type*: #/definitions/CancelOrderResponse
**Examples**:
/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Order/modify
**Operation ID**: Order_ModifyOrder
**Summary**: Modifies an existing order based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Order/modify`
**Parameters**:
- **request** (body): object - A request containing order modification details.
  - *Required*
  - *Type Reference*: #/definitions/ModifyOrderRequest
**Responses**:
- **200**: A response with order modification details.
  - *Response Type*: #/definitions/ModifyOrderResponse
**Examples**:
/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Position Module

**Total Endpoints**: 3

### POST /api/Position/searchOpen
**Operation ID**: Position_SearchOpenPositions
**Summary**: Searches for open positions based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Position/searchOpen`
**Parameters**:
- **request** (body): object - A request containing search criteria.
  - *Required*
  - *Type Reference*: #/definitions/SearchPositionRequest
**Responses**:
- **200**: A response with search results.
  - *Response Type*: #/definitions/SearchPositionResponse
**Examples**:
/api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Position/closeContract
**Operation ID**: Position_CloseContractPosition
**Summary**: Closes a contract position based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Position/closeContract`
**Parameters**:
- **request** (body): object - A request containing the account ID and contract ID to close.
  - *Required*
  - *Type Reference*: #/definitions/CloseContractPositionRequest
**Responses**:
- **200**: A response indicating the success or failure of the close operation.
  - *Response Type*: #/definitions/ClosePositionResponse
**Examples**:
/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

### POST /api/Position/partialCloseContract
**Operation ID**: Position_PartialCloseContractPosition
**Summary**: Partially closes a contract position based on the provided request.
**Full URL**: `https://api.topstepx.com/api/Position/partialCloseContract`
**Parameters**:
- **request** (body): object - A request containing the account ID, contract ID, and size to close.
  - *Required*
  - *Type Reference*: #/definitions/PartialCloseContractPositionRequest
**Responses**:
- **200**: A response indicating the success or failure of the partial close operation.
  - *Response Type*: #/definitions/PartialClosePositionResponse
**Examples**:
/api/Position/partialCloseContract
Description: Partially close a position.
Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
  - Name: contractId
    Type: string
    Description: The contract ID.
    Required: true
  - Name: size
    Type: integer
    Description: The size to close.
    Required: true
```

----------------------------------------

TITLE: Example cURL Request to Partially Close Position
DESCRIPTION: Demonstrates how to send a POST request using cURL to partially close a position. The request includes necessary headers and a JSON body specifying the account ID, contract ID, and the size to close.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/partialCloseContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25",
    "size": 1
  }'
```

----------------------------------------

TITLE: API Endpoint Definition for Close Positions
DESCRIPTION: Defines the HTTP method, URL, and required parameters for the 'Close Positions' API endpoint, which allows users to close an open position.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: APIDOC
CODE:
```
POST https://api.topstepx.com/api/Position/closeContract

Parameters:
  accountId (integer, Required): The account ID.
  contractId (string, Required): The contract ID.
```

----------------------------------------

TITLE: cURL Example Request to Close a Position
DESCRIPTION: A cURL command demonstrating how to send a POST request to the 'Close Positions' endpoint. It includes necessary headers and a JSON body with example `accountId` and `contractId` values.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/closeContract' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536,
    "contractId": "CON.F.US.GMET.J25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Close Position
DESCRIPTION: An example of a successful JSON response received after a 'Close Position' request. This structure indicates the operation's success status, an error code, and any associated error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Search for Contracts API Definition
DESCRIPTION: Defines the API endpoint for searching contracts, including the HTTP method, URL, and detailed descriptions of the 'searchText' and 'live' parameters, specifying their types, requirements, and nullability.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/search

Parameters:
  searchText:
    Type: string
    Description: The name of the contract to search for.
    Required: true
    Nullable: false
  live:
    Type: boolean
    Description: Whether to search for contracts using the sim/live data subscription.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Contracts
DESCRIPTION: Demonstrates how to make a POST request to the 'Search for Contracts' API endpoint using cURL, including setting headers and providing the request body with 'live' and 'searchText' parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "live": false,
    "searchText": "NQ"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Contracts
DESCRIPTION: Illustrates a successful response from the 'Search for Contracts' API, showing an array of contract objects with details such as ID, name, description, tick size, and whether the contract is active, along with success status and error information.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: json
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.MNQ.H25",
            "name": "MNQH25",
            "description": "Micro E-mini Nasdaq-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 0.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQG.G25",
            "name": "NQGG25",
            "description": "E-Mini Natural Gas: February 2025",
            "tickSize": 0.005,
            "tickValue": 12.5,
            "activeContract": true
        },
        {
            "id": "CON.F.US.NQM.G25",
            "name": "NQMG25",
            "description": "E-Mini Crude Oil: February 2025",
            "tickSize": 0.025,
            "tickValue": 12.5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Endpoint: Search Contract by ID
DESCRIPTION: Documents the API endpoint for searching a contract by its unique identifier. It details the URL, HTTP method, required parameters, and their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Contract/searchById
Parameters:
  contractId:
    Type: string
    Description: The id of the contract to search for.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: cURL Example Request for Search Contract by ID
DESCRIPTION: Demonstrates how to make a POST request to the `searchById` endpoint using cURL. This example includes the API URL, necessary headers (accept, Content-Type), and a JSON request body specifying the `contractId`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Contract/searchById' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.ENQ.H25"
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Search Contract by ID
DESCRIPTION: Illustrates a successful JSON response from the `searchById` endpoint. It shows the structure of the returned `contracts` array, including contract details like ID, name, description, tick size, and active status, along with `success`, `errorCode`, and `errorMessage` fields.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: JSON
CODE:
```
{
    "contracts": [
        {
            "id": "CON.F.US.ENQ.H25",
            "name": "ENQH25",
            "description": "E-mini NASDAQ-100: March 2025",
            "tickSize": 0.25,
            "tickValue": 5,
            "activeContract": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example cURL Request for Trade Search
DESCRIPTION: Demonstrates how to make a POST request to the ProjectX Gateway API's 'Search for Trades' endpoint using cURL, including headers and a JSON request body with example account and timestamp filters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Status Module

**Total Endpoints**: 1

### GET /api/Status/ping
**Operation ID**: Status_Ping
**Summary**: Handles the ping request to check the status of the API.
**Full URL**: `https://api.topstepx.com/api/Status/ping`
**Responses**:
- **200**: Returns a "pong" to indicate the API is responsive.

## Trade Module

**Total Endpoints**: 1

### POST /api/Trade/search
**Operation ID**: Trade_SearchHalfTurnTrades
**Summary**: Searches for half-turn trades based on the provided request parameters.
**Full URL**: `https://api.topstepx.com/api/Trade/search`
**Parameters**:
- **request** (body): object - A request containing search criteria.
  - *Required*
  - *Type Reference*: #/definitions/SearchTradeRequest
**Responses**:
- **200**: A response with the search results.
  - *Response Type*: #/definitions/SearchHalfTradeResponse
**Examples**:
/api/Trade/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 203,
    "startTimestamp": "2025-01-20T15:47:39.882Z",
    "endTimestamp": "2025-01-30T15:47:39.882Z"
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Trade Search
DESCRIPTION: Provides a sample JSON response body for a successful 'Search for Trades' API call, illustrating the structure and typical values of returned trade objects, including `id`, `accountId`, `price`, `profitAndLoss`, and `fees`.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: JSON
CODE:
```
{
    "trades": [
        {
            "id": 8604,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:52.523293+00:00",
            "price": 6065.250000000,
            "profitAndLoss": 50.000000000,
            "fees": 1.4000,
            "side": 1,
            "size": 1,
            "voided": false,
            "orderId": 14328
        },
        {
            "id": 8603,
            "accountId": 203,
            "contractId": "CON.F.US.EP.H25",
            "creationTimestamp": "2025-01-21T16:13:04.142302+00:00",
            "price": 6064.250000000,
            "profitAndLoss": null,
            "fees": 1.4000,
            "side": 0,
            "size": 1,
            "voided": false,
            "orderId": 14326
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Trades Endpoint
DESCRIPTION: Defines the 'Search for Trades' API endpoint, including its method, URL, required and optional request parameters, and the schema for a successful response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: APIDOC
CODE:
```
API: Search for Trades
Method: POST
URL: https://api.topstepx.com/api/Trade/search
Description: Search for trades from the request parameters.

Request Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  startTimestamp:
    Type: datetime
    Description: The start of the timestamp filter.
    Required: true
    Nullable: false
  endTimestamp:
    Type: datetime
    Description: The end of the timestamp filter.
    Required: false
    Nullable: true

Response Schema (Success):
  trades: array of objects
    id: integer
    accountId: integer
    contractId: string
    creationTimestamp: datetime
    price: decimal
    profitAndLoss: decimal (nullable: true, indicates a half-turn trade if null)
    fees: decimal
    side: integer
    size: integer
    voided: boolean
    orderId: integer
  success: boolean
  errorCode: integer
  errorMessage: string (nullable: true)
```

----------------------------------------

TITLE: API Reference: Search for Positions Endpoint
DESCRIPTION: Comprehensive documentation for the POST /api/Position/searchOpen endpoint, detailing its purpose, required parameters, and the structure of the request and response.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: APIDOC
CODE:
```
Endpoint: POST https://api.topstepx.com/api/Position/searchOpen
Description: Search for open positions.

Parameters:
  - Name: accountId
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false

Request Body Schema:
  {
    "accountId": <integer> // The account ID to search for positions.
  }

Success Response Schema:
  {
    "positions": [
      {
        "id": <integer>,
        "accountId": <integer>,
        "contractId": <string>, // e.g., "CON.F.US.GMET.J25"
        "creationTimestamp": <string (ISO 8601 datetime)>, // e.g., "2025-04-21T19:52:32.175721+00:00"
        "type": <integer>, // Position type
        "size": <integer>, // Size of the position
        "averagePrice": <number> // Average price of the position
      }
    ],
    "success": <boolean>,
    "errorCode": <integer>,
    "errorMessage": <string | null>
  }

Error Response Schema:
  - Status: 401 Unauthorized
  - Message: <string> // e.g., "Error: response status is 401"
```

----------------------------------------

TITLE: cURL Request to Search Open Positions
DESCRIPTION: Demonstrates how to send a POST request to the `/api/Position/searchOpen` endpoint using cURL, including setting headers and providing the `accountId` in the request body.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Position/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 536
  }'
```

----------------------------------------

TITLE: JSON Success Response for Search Open Positions
DESCRIPTION: An example of a successful JSON response from the `/api/Position/searchOpen` endpoint, showing a list of open positions with their detailed attributes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: json
CODE:
```
{
    "positions": [
        {
            "id": 6124,
            "accountId": 536,
            "contractId": "CON.F.US.GMET.J25",
            "creationTimestamp": "2025-04-21T19:52:32.175721+00:00",
            "type": 1,
            "size": 2,
            "averagePrice": 1575.750000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: ProjectX Gateway API: Retrieve Bars Endpoint Definition
DESCRIPTION: Documents the API endpoint for retrieving historical market data bars, including its URL, HTTP method, and detailed request parameters with their types, descriptions, and constraints.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/History/retrieveBars
Description: Retrieve bars.

Parameters:
  contractId: integer (Required, Not Nullable)
    Description: The contract ID.
  live: boolean (Required, Not Nullable)
    Description: Whether to retrieve bars using the sim or live data subscription.
  startTime: datetime (Required, Not Nullable)
    Description: The start time of the historical data.
  endTime: datetime (Required, Not Nullable)
    Description: The end time of the historical data.
  unit: integer (Required, Not Nullable)
    Description: The unit of aggregation for the historical data:
      1 = Second
      2 = Minute
      3 = Hour
      4 = Day
      5 = Week
      6 = Month
  unitNumber: integer (Required, Not Nullable)
    Description: The number of units to aggregate.
  limit: integer (Required, Not Nullable)
    Description: The maximum number of bars to retrieve.
  includePartialBar: boolean (Required, Not Nullable)
    Description: Whether to include a partial bar representing the current time unit.
```

----------------------------------------

TITLE: cURL Example Request to Retrieve Bars
DESCRIPTION: Demonstrates how to make a POST request to the /api/History/retrieveBars endpoint using cURL, providing example parameters for historical data retrieval such as contract ID, time range, and aggregation unit.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/History/retrieveBars' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "contractId": "CON.F.US.RTY.Z24",
    "live": false,
    "startTime": "2024-12-01T00:00:00Z",
    "endTime": "2024-12-31T21:00:00Z",
    "unit": 3,
    "unitNumber": 1,
    "limit": 7,
    "includePartialBar": false
  }'
```

----------------------------------------

TITLE: JSON Example Success Response for Retrieve Bars
DESCRIPTION: Illustrates the structure of a successful JSON response from the Retrieve Bars API, containing an array of historical bar data with timestamp, open, high, low, close, and volume, along with success status and error codes.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
{
    "bars": [
        {
            "t": "2024-12-20T14:00:00+00:00",
            "o": 2208.100000000,
            "h": 2217.000000000,
            "l": 2206.700000000,
            "c": 2210.100000000,
            "v": 87
        },
        {
            "t": "2024-12-20T13:00:00+00:00",
            "o": 2195.800000000,
            "h": 2215.000000000,
            "l": 2192.900000000,
            "c": 2209.800000000,
            "v": 536
        },
        {
            "t": "2024-12-20T12:00:00+00:00",
            "o": 2193.600000000,
            "h": 2200.300000000,
            "l": 2192.000000000,
            "c": 2198.000000000,
            "v": 180
        },
        {
            "t": "2024-12-20T11:00:00+00:00",
            "o": 2192.200000000,
            "h": 2194.800000000,
            "l": 2189.900000000,
            "c": 2194.800000000,
            "v": 174
        },
        {
            "t": "2024-12-20T10:00:00+00:00",
            "o": 2200.400000000,
            "h": 2200.400000000,
            "l": 2191.000000000,
            "c": 2193.100000000,
            "v": 150
        },
        {
            "t": "2024-12-20T09:00:00+00:00",
            "o": 2205.000000000,
            "h": 2205.800000000,
            "l": 2198.900000000,
            "c": 2200.500000000,
            "v": 56
        },
        {
            "t": "2024-12-20T08:00:00+00:00",
            "o": 2207.700000000,
            "h": 2210.100000000,
            "l": 2198.100000000,
            "c": 2204.900000000,
            "v": 144
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Documentation for Account Search Endpoint
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, detailing its HTTP method, URL, required parameters, and the structure of successful and error responses.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search

Parameters:
  - Name: onlyActiveAccounts
    Type: boolean
    Description: Whether to filter only active accounts.
    Required: true
    Nullable: false

Example Success Response:
  {
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "balance": 50000,
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
  }

Example Error Response:
  Error: response status is 401
```

----------------------------------------

TITLE: cURL Example Request for Account Search API
DESCRIPTION: A cURL command demonstrating how to invoke the Account search API endpoint with the specified headers.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: curl
CODE:
```
curl -X 'undefined' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: JSON Example Success Response for Account Search
DESCRIPTION: An example of a successful JSON response returned by the Account search API, showing a list of accounts with their details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
{
  "accounts": [
      {
          "id": 1,
          "name": "TEST_ACCOUNT_1",
          "balance": 50000,
          "canTrade": true,
          "isVisible": true
      }
  ],
  "success": true,
  "errorCode": 0,
  "errorMessage": null
}
```

----------------------------------------

TITLE: Connect to SignalR Market Hub and Subscribe to Market Data
DESCRIPTION: This JavaScript code illustrates how to connect to the SignalR Market Hub. It configures the connection with a JWT token and automatic reconnection, then subscribes to real-time market data including contract quotes, trades, and market depth for a specified contract ID. Event handlers are provided to process and log the incoming market updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const marketHubUrl = 'https://rtc.topstepx.com/hubs/market?access_token=YOUR_JWT_TOKEN';
    const CONTRACT_ID = 'CON.F.US.RTY.H25';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(marketHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('SubscribeContractMarketDepth', CONTRACT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeContractQuotes', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractTrades', CONTRACT_ID);
                rtcConnection.invoke('UnsubscribeContractMarketDepth', CONTRACT_ID);
            };

            rtcConnection.on('GatewayQuote', (contractId, data)  => {
                console.log('Received market quote data', data);
            });

            rtcConnection.on('GatewayTrade', (contractId, data) => {
                console.log('Received market trade data', data);
            });

            rtcConnection.on('GatewayDepth', (contractId, data) => {
                console.log('Received market depth data', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: Connect to SignalR User Hub and Subscribe to Account Data
DESCRIPTION: This JavaScript code demonstrates how to establish a SignalR connection to the User Hub. It initializes the connection with a JWT token, sets up automatic reconnection, and subscribes to real-time updates for user accounts, orders, positions, and trades. It also includes event handlers for receiving and logging these updates.
SOURCE: https://gateway.docs.projectx.com/docs/realtime

LANGUAGE: JavaScript
CODE:
```
// Import the necessary modules from @microsoft/signalr
const { HubConnectionBuilder, HttpTransportType } = require('@microsoft/signalr');

function setupSignalRConnection() {
    const JWT_TOKEN = 'your_bearer_token';
    const SELECTED_ACCOUNT_ID = 123; // your currently selected/visible account ID
    const userHubUrl = 'https://rtc.topstepx.com/hubs/user?access_token=YOUR_JWT_TOKEN';

    const rtcConnection = new HubConnectionBuilder()
        .withUrl(userHubUrl, {
            skipNegotiation: true,
            transport: HttpTransportType.WebSockets,
            accessTokenFactory: () => JWT_TOKEN,
            timeout: 10000
        })
        .withAutomaticReconnect()
        .build();

    rtcConnection.start()
        .then(() => {
            const subscribe = () => {
                rtcConnection.invoke('SubscribeAccounts');
                rtcConnection.invoke('SubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('SubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            const unsubscribe = () => {
                rtcConnection.invoke('UnsubscribeAccounts');
                rtcConnection.invoke('UnsubscribeOrders', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribePositions', SELECTED_ACCOUNT_ID);
                rtcConnection.invoke('UnsubscribeTrades', SELECTED_ACCOUNT_ID);
            };

            rtcConnection.on('GatewayUserAccount', (data) => {
                console.log('Received account update', data);
            });

            rtcConnection.on('GatewayUserOrder', (data) => {
                console.log('Received order update', data);
            });

            rtcConnection.on('GatewayUserPosition', (data) => {
                console.log('Received position update', data);
            });

            rtcConnection.on('GatewayUserTrade', (data) => {
                console.log('Received trade update', data);
            });

            subscribe();

            rtcConnection.onreconnected((connectionId) => {
                console.log('RTC Connection Reconnected');
                subscribe();
            });
        })
        .catch((err) => {
            console.error('Error starting connection:', err);
        });
}
// Call the function to set up and start the connection
setupSignalRConnection();

```

----------------------------------------

TITLE: ProjectX Real Time API Overview
DESCRIPTION: Details the ProjectX Real Time API, which leverages the SignalR library over WebSocket to deliver real-time updates for critical financial data points such as accounts, orders, positions, balances, and quotes.
SOURCE: https://gateway.docs.projectx.com/docs/category/realtime-updates

LANGUAGE: APIDOC
CODE:
```
ProjectX Real Time API:
  Description: Provides real-time access to data updates.
  Technology: SignalR library (via WebSocket)
  Capabilities:
    - Real-time access to data updates
  Data Types:
    - Accounts
    - Orders
    - Positions
    - Balances
    - Quotes
```

----------------------------------------

TITLE: API Operation: Search Active Accounts
DESCRIPTION: Comprehensive documentation for the /api/Account/search endpoint, including its URL, reference, example request and response JSON payloads, and a cURL command for demonstration. This endpoint is used to retrieve a list of active trading accounts.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/placing-your-first-order

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Account/search
API Reference: /api/Account/search (Swagger link)
```

LANGUAGE: json
CODE:
```
{
  "onlyActiveAccounts": true
}
```

LANGUAGE: json
CODE:
```
{
    "accounts": [
        {
            "id": 1,
            "name": "TEST_ACCOUNT_1",
            "canTrade": true,
            "isVisible": true
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

LANGUAGE: curl
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Account/search' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "onlyActiveAccounts": true
  }'
```

----------------------------------------

TITLE: API Reference: Cancel an Order Endpoint
DESCRIPTION: Defines the API endpoint, HTTP method, and required parameters for cancelling an order within the ProjectX Gateway API. This section outlines the structure for making a valid API call.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/cancel
API Reference: /api/Order/cancel

Description: Cancel an order.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
  orderId:
    Type: integer
    Description: The order id.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example Request: Cancel Order using cURL
DESCRIPTION: Illustrates how to construct and execute a POST request using cURL to cancel an order. This example includes the target URL, necessary headers, and the JSON payload containing account and order IDs.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/cancel' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974
  }'
```

----------------------------------------

TITLE: Example Response: Successful Order Cancellation
DESCRIPTION: Provides an example of the JSON response received when an order cancellation request is processed successfully, indicating the operation's status, error code, and message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Modify an Order Endpoint
DESCRIPTION: Comprehensive documentation for the ProjectX Gateway API's Order modification endpoint, detailing the HTTP method, URL, and all required and optional parameters with their types and descriptions.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: APIDOC
CODE:
```
API Endpoint: POST https://api.topstepx.com/api/Order/modify
Description: Modify an open order.

Parameters:
- accountId: integer (Required, Not Nullable)
  Description: The account ID.
- orderId: integer (Required, Not Nullable)
  Description: The order id.
- size: integer (Optional, Nullable)
  Description: The size of the order.
- limitPrice: decimal (Optional, Nullable)
  Description: The limit price for the order, if applicable.
- stopPrice: decimal (Optional, Nullable)
  Description: The stop price for the order, if applicable.
- trailPrice: decimal (Optional, Nullable)
  Description: The trail price for the order, if applicable.
```

----------------------------------------

TITLE: cURL Request Example: Modify Order
DESCRIPTION: Demonstrates how to send a POST request using cURL to the ProjectX Gateway API's order modification endpoint, including necessary headers and a JSON payload with order details.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: bash
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/modify' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 465,
    "orderId": 26974,
    "size": 1,
    "limitPrice": null,
    "stopPrice": 1604,
    "trailPrice": null
  }'
```

----------------------------------------

TITLE: JSON Response Example: Successful Order Modification
DESCRIPTION: An example of a successful JSON response received after modifying an order, indicating the operation's success status, a zero error code, and a null error message.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: json
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: API Reference: Search for Open Orders Endpoint
DESCRIPTION: Comprehensive documentation for the Search Open Orders API endpoint, detailing its URL, HTTP method, purpose, and required parameters.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Order/searchOpen
API Reference: /api/Order/searchOpen

Description:
  Search for open orders.

Parameters:
  accountId:
    Type: integer
    Description: The account ID.
    Required: true
    Nullable: false
```

----------------------------------------

TITLE: Example cURL Request to Search Open Orders
DESCRIPTION: Demonstrates how to make a POST request to the Search Open Orders API using cURL, including necessary headers and the JSON request body with the account ID.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Order/searchOpen' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json' \
  -d '{
    "accountId": 212
  }'
```

----------------------------------------

TITLE: Example JSON Success Response for Search Open Orders
DESCRIPTION: Illustrates the structure of a successful JSON response from the Search Open Orders API, containing an array of order objects with various details like ID, account, contract, timestamps, status, type, side, size, and prices.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: JSON
CODE:
```
{
    "orders": [
        {
            "id": 26970,
            "accountId": 212,
            "contractId": "CON.F.US.EP.M25",
            "creationTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "updateTimestamp": "2025-04-21T19:45:52.105808+00:00",
            "status": 1,
            "type": 4,
            "side": 1,
            "size": 1,
            "limitPrice": null,
            "stopPrice": 5138.000000000
        }
    ],
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Order Placement
DESCRIPTION: An example of a generic error response, specifically indicating an HTTP 401 Unauthorized status, which might occur if authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-place

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response from Order Search API
DESCRIPTION: Shows a typical error message returned by the ProjectX Gateway API's Order search endpoint, specifically indicating an authentication failure (HTTP 401 Unauthorized) when the request is not properly authenticated.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example JSON Success Response for Partial Close
DESCRIPTION: Shows a successful JSON response from the partial close position API. A 'success' status of true indicates that the operation was completed without errors, with 'errorCode' being 0 and 'errorMessage' null.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: JSON
CODE:
```
{
    "success": true,
    "errorCode": 0,
    "errorMessage": null
}
```

----------------------------------------

TITLE: Example Error Response for Partial Close
DESCRIPTION: Illustrates a common error response for the partial close position API. This specific example indicates a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions-partial

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response: Unauthorized
DESCRIPTION: An example of a generic error response, specifically indicating an unauthorized (HTTP 401) status. This typically occurs when authentication fails.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/close-positions

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Search Contracts
DESCRIPTION: Provides an example of a generic error message returned by the API, indicating a non-successful status, such as an authentication failure (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Contract by ID
DESCRIPTION: Provides an example of a common error response, specifically indicating a 401 Unauthorized status, which typically occurs due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/search-contracts-by-id

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Error Response for Trade Search
DESCRIPTION: Shows a common error message returned by the 'Search for Trades' API, specifically for an unauthorized request (HTTP 401 status).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/trade/trade-search

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Error Response Example for Search Open Positions
DESCRIPTION: An example of an error response, specifically indicating a 401 Unauthorized status, which might occur due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/positions/search-open-positions

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Retrieve Bars
DESCRIPTION: Shows an example of an error response from the Retrieve Bars API, indicating a 401 Unauthorized status, typically due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/market-data/retrieve-bars

LANGUAGE: JSON
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: JSON Example Error Response for Account Search
DESCRIPTION: An example of an error response from the Account search API, typically indicating an unauthorized access (HTTP 401).
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/account/search-accounts

LANGUAGE: json
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Example Response: Order Cancellation Error
DESCRIPTION: Shows an example of a plain text error response, specifically a 401 Unauthorized status, which indicates a failure in the order cancellation process, often due to authentication issues.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-cancel

LANGUAGE: Text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: Text Response Example: Order Modification Error (401)
DESCRIPTION: An example of an error response, specifically a 401 Unauthorized status, which might occur if authentication fails during an attempt to modify an order.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-modify

LANGUAGE: text
CODE:
```
Error: response status is 401
```

----------------------------------------

TITLE: ProjectX Gateway API: Validate Session Endpoint
DESCRIPTION: Details the API endpoint for validating session tokens, including the URL, HTTP method, and a link to the full Swagger documentation for the Auth_Validate operation.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: APIDOC
CODE:
```
API URL: POST https://api.topstepx.com/api/Auth/validate
API Reference: /api/Auth/validate (https://api.topstepx.com/swagger/index.html#/Auth/Auth_Validate)
```

----------------------------------------

TITLE: Validate Session Token using cURL
DESCRIPTION: Example cURL command to send a POST request to the ProjectX Gateway API's /Auth/validate endpoint. It includes setting necessary headers for content type (application/json) and acceptance (text/plain).
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: cURL
CODE:
```
curl -X 'POST' \
  'https://api.topstepx.com/api/Auth/validate' \
  -H 'accept: text/plain' \
  -H 'Content-Type: application/json'
```

----------------------------------------

TITLE: Example JSON Response for Session Validation
DESCRIPTION: Illustrates the expected JSON response from the ProjectX Gateway API after a successful session token validation. The response includes a success status, error codes, and the new token.
SOURCE: https://gateway.docs.projectx.com/docs/getting-started/validate-session

LANGUAGE: JSON
CODE:
```
{
  "success": true,
  "errorCode": 0,
  "errorMessage": null,
  "newToken": "NEW_TOKEN"
}
```

----------------------------------------

TITLE: Example Error Response for Search Open Orders
DESCRIPTION: Shows a typical error response when the API request fails, specifically indicating a 401 Unauthorized status.
SOURCE: https://gateway.docs.projectx.com/docs/api-reference/order/order-search-open

LANGUAGE: Plaintext
CODE:
```
Error: response status is 401
```

## Data Models Reference

### SearchAccountResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **accounts**: array - No description

### SearchAccountErrorCode

**Type**: integer

**Description**: 0 = Success

**Possible Values**: 0

### TradingAccountModel

**Type**: object

**Properties**:

- **id**: integer - No description

  - *Required*

- **name**: string - No description

  - *Required*

- **balance**: number - No description

  - *Required*

- **canTrade**: boolean - No description

  - *Required*

- **isVisible**: boolean - No description

  - *Required*

- **simulated**: boolean - No description

  - *Required*

### SearchAccountRequest

**Type**: object

**Properties**:

- **onlyActiveAccounts**: boolean - No description

  - *Required*

### LoginResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **token**: string - No description

### LoginErrorCode

**Type**: integer

**Description**: 0 = Success
1 = UserNotFound
2 = PasswordVerificationFailed
3 = InvalidCredentials
4 = AppNotFound
5 = AppVerificationFailed
6 = InvalidDevice
7 = AgreementsNotSigned
8 = UnknownError
9 = ApiSubscriptionNotFound
10 = ApiKeyAuthenticationDisabled

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10

### LoginAppRequest

**Type**: object

**Properties**:

- **userName**: string - No description

  - *Required*

- **password**: string - No description

  - *Required*

- **deviceId**: string - No description

  - *Required*

- **appId**: string - No description

  - *Required*

- **verifyKey**: string - No description

  - *Required*

### LoginApiKeyRequest

**Type**: object

**Properties**:

- **userName**: string - No description

  - *Required*

- **apiKey**: string - No description

  - *Required*

### LogoutResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

### LogoutErrorCode

**Type**: integer

**Description**: 0 = Success
1 = InvalidSession
2 = UnknownError

**Possible Values**: 0, 1, 2

### ValidateResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **newToken**: string - No description

### ValidateErrorCode

**Type**: integer

**Description**: 0 = Success
1 = InvalidSession
2 = SessionNotFound
3 = ExpiredToken
4 = UnknownError

**Possible Values**: 0, 1, 2, 3, 4

### SearchContractResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **contracts**: array - No description

### SearchContractErrorCode

**Type**: integer

**Description**: 0 = Success

**Possible Values**: 0

### ContractModel

**Type**: object

**Properties**:

- **id**: string - No description

  - *Required*

- **name**: string - No description

  - *Required*

- **description**: string - No description

  - *Required*

- **tickSize**: number - No description

  - *Required*

- **tickValue**: number - No description

  - *Required*

- **activeContract**: boolean - No description

  - *Required*

### SearchContractRequest

**Type**: object

**Properties**:

- **searchText**: string - No description

- **live**: boolean - No description

  - *Required*

### SearchContractByIdResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **contract**: unknown - No description

### SearchContractByIdErrorCode

**Type**: integer

**Description**: 0 = Success
1 = ContractNotFound

**Possible Values**: 0, 1

### SearchContractByIdRequest

**Type**: object

**Properties**:

- **contractId**: string - No description

  - *Required*

### RetrieveBarResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **bars**: array - No description

### RetrieveBarErrorCode

**Type**: integer

**Description**: 0 = Success
1 = ContractNotFound
2 = UnitInvalid
3 = UnitNumberInvalid
4 = LimitInvalid

**Possible Values**: 0, 1, 2, 3, 4

### AggregateBarModel

**Type**: object

**Properties**:

- **t**: string - No description

  - *Required*

- **o**: number - No description

  - *Required*

- **h**: number - No description

  - *Required*

- **l**: number - No description

  - *Required*

- **c**: number - No description

  - *Required*

- **v**: integer - No description

  - *Required*

### RetrieveBarRequest

**Type**: object

**Properties**:

- **contractId**: string - No description

  - *Required*

- **live**: boolean - No description

  - *Required*

- **startTime**: string - No description

  - *Required*

- **endTime**: string - No description

  - *Required*

- **unit**: unknown - No description

  - *Required*

- **unitNumber**: integer - No description

  - *Required*

- **limit**: integer - No description

  - *Required*

- **includePartialBar**: boolean - No description

  - *Required*

### AggregateBarUnit

**Type**: integer

**Description**: 0 = Unspecified
1 = Second
2 = Minute
3 = Hour
4 = Day
5 = Week
6 = Month

**Possible Values**: 0, 1, 2, 3, 4, 5, 6

### SearchOrderResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **orders**: array - No description

### SearchOrderErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound

**Possible Values**: 0, 1

### OrderModel

**Type**: object

**Properties**:

- **id**: integer - No description

  - *Required*

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

- **creationTimestamp**: string - No description

  - *Required*

- **updateTimestamp**: string - No description

- **status**: unknown - No description

  - *Required*

- **type**: unknown - No description

  - *Required*

- **side**: unknown - No description

  - *Required*

- **size**: integer - No description

  - *Required*

- **limitPrice**: number - No description

- **stopPrice**: number - No description

- **fillVolume**: integer - No description

  - *Required*

### OrderStatus

**Type**: integer

**Description**: 0 = None
1 = Open
2 = Filled
3 = Cancelled
4 = Expired
5 = Rejected
6 = Pending

**Possible Values**: 0, 1, 2, 3, 4, 5, 6

### OrderType

**Type**: integer

**Description**: 0 = Unknown
1 = Limit
2 = Market
3 = StopLimit
4 = Stop
5 = TrailingStop
6 = JoinBid
7 = JoinAsk

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7

### OrderSide

**Type**: integer

**Description**: 0 = Bid
1 = Ask

**Possible Values**: 0, 1

### SearchOrderRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **startTimestamp**: string - No description

  - *Required*

- **endTimestamp**: string - No description

### SearchOpenOrderRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

### PlaceOrderResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **orderId**: integer - No description

### PlaceOrderErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound
2 = OrderRejected
3 = InsufficientFunds
4 = AccountViolation
5 = OutsideTradingHours
6 = OrderPending
7 = UnknownError
8 = ContractNotFound
9 = ContractNotActive
10 = AccountRejected

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10

### PlaceOrderRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

- **type**: unknown - No description

  - *Required*

- **side**: unknown - No description

  - *Required*

- **size**: integer - No description

  - *Required*

- **limitPrice**: number - No description

- **stopPrice**: number - No description

- **trailPrice**: number - No description

- **customTag**: string - No description

- **linkedOrderId**: integer - No description

### CancelOrderResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

### CancelOrderErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound
2 = OrderNotFound
3 = Rejected
4 = Pending
5 = UnknownError
6 = AccountRejected

**Possible Values**: 0, 1, 2, 3, 4, 5, 6

### CancelOrderRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **orderId**: integer - No description

  - *Required*

### ModifyOrderResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

### ModifyOrderErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound
2 = OrderNotFound
3 = Rejected
4 = Pending
5 = UnknownError
6 = AccountRejected
7 = ContractNotFound

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7

### ModifyOrderRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **orderId**: integer - No description

  - *Required*

- **size**: integer - No description

- **limitPrice**: number - No description

- **stopPrice**: number - No description

- **trailPrice**: number - No description

### SearchPositionResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **positions**: array - No description

### SearchPositionErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound

**Possible Values**: 0, 1

### PositionModel

**Type**: object

**Properties**:

- **id**: integer - No description

  - *Required*

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

- **creationTimestamp**: string - No description

  - *Required*

- **type**: unknown - No description

  - *Required*

- **size**: integer - No description

  - *Required*

- **averagePrice**: number - No description

  - *Required*

### PositionType

**Type**: integer

**Description**: 0 = Undefined
1 = Long
2 = Short

**Possible Values**: 0, 1, 2

### SearchPositionRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

### ClosePositionResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

### ClosePositionErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound
2 = PositionNotFound
3 = ContractNotFound
4 = ContractNotActive
5 = OrderRejected
6 = OrderPending
7 = UnknownError
8 = AccountRejected

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7, 8

### CloseContractPositionRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

### PartialClosePositionResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

### PartialClosePositionErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound
2 = PositionNotFound
3 = ContractNotFound
4 = ContractNotActive
5 = InvalidCloseSize
6 = OrderRejected
7 = OrderPending
8 = UnknownError
9 = AccountRejected

**Possible Values**: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9

### PartialCloseContractPositionRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

- **size**: integer - No description

  - *Required*

### SearchHalfTradeResponse

**Type**: object

**Properties**:

- **success**: boolean - No description

  - *Required*

- **errorCode**: unknown - No description

  - *Required*

- **errorMessage**: string - No description

- **trades**: array - No description

### SearchTradeErrorCode

**Type**: integer

**Description**: 0 = Success
1 = AccountNotFound

**Possible Values**: 0, 1

### HalfTradeModel

**Type**: object

**Properties**:

- **id**: integer - No description

  - *Required*

- **accountId**: integer - No description

  - *Required*

- **contractId**: string - No description

  - *Required*

- **creationTimestamp**: string - No description

  - *Required*

- **price**: number - No description

  - *Required*

- **profitAndLoss**: number - No description

- **fees**: number - No description

  - *Required*

- **side**: unknown - No description

  - *Required*

- **size**: integer - No description

  - *Required*

- **voided**: boolean - No description

  - *Required*

- **orderId**: integer - No description

  - *Required*

### SearchTradeRequest

**Type**: object

**Properties**:

- **accountId**: integer - No description

  - *Required*

- **startTimestamp**: string - No description

- **endTimestamp**: string - No description

## Additional Examples and Usage Patterns

## Error Codes Reference

### SearchAccountErrorCode

0 = Success

- **0**: Success

### LoginErrorCode

0 = Success
1 = UserNotFound
2 = PasswordVerificationFailed
3 = InvalidCredentials
4 = AppNotFound
5 = AppVerificationFailed
6 = InvalidDevice
7 = AgreementsNotSigned
8 = UnknownError
9 = ApiSubscriptionNotFound
10 = ApiKeyAuthenticationDisabled

- **0**: Success

- **1**: UserNotFound

- **2**: PasswordVerificationFailed

- **3**: InvalidCredentials

- **4**: AppNotFound

- **5**: AppVerificationFailed

- **6**: InvalidDevice

- **7**: AgreementsNotSigned

- **8**: UnknownError

- **9**: ApiSubscriptionNotFound

- **10**: ApiKeyAuthenticationDisabled

### LogoutErrorCode

0 = Success
1 = InvalidSession
2 = UnknownError

- **0**: Success

- **1**: InvalidSession

- **2**: UnknownError

### ValidateErrorCode

0 = Success
1 = InvalidSession
2 = SessionNotFound
3 = ExpiredToken
4 = UnknownError

- **0**: Success

- **1**: InvalidSession

- **2**: SessionNotFound

- **3**: ExpiredToken

- **4**: UnknownError

### SearchContractErrorCode

0 = Success

- **0**: Success

### SearchContractByIdErrorCode

0 = Success
1 = ContractNotFound

- **0**: Success

- **1**: ContractNotFound

### RetrieveBarErrorCode

0 = Success
1 = ContractNotFound
2 = UnitInvalid
3 = UnitNumberInvalid
4 = LimitInvalid

- **0**: Success

- **1**: ContractNotFound

- **2**: UnitInvalid

- **3**: UnitNumberInvalid

- **4**: LimitInvalid

### SearchOrderErrorCode

0 = Success
1 = AccountNotFound

- **0**: Success

- **1**: AccountNotFound

### PlaceOrderErrorCode

0 = Success
1 = AccountNotFound
2 = OrderRejected
3 = InsufficientFunds
4 = AccountViolation
5 = OutsideTradingHours
6 = OrderPending
7 = UnknownError
8 = ContractNotFound
9 = ContractNotActive
10 = AccountRejected

- **0**: Success

- **1**: AccountNotFound

- **2**: OrderRejected

- **3**: InsufficientFunds

- **4**: AccountViolation

- **5**: OutsideTradingHours

- **6**: OrderPending

- **7**: UnknownError

- **8**: ContractNotFound

- **9**: ContractNotActive

- **10**: AccountRejected

### CancelOrderErrorCode

0 = Success
1 = AccountNotFound
2 = OrderNotFound
3 = Rejected
4 = Pending
5 = UnknownError
6 = AccountRejected

- **0**: Success

- **1**: AccountNotFound

- **2**: OrderNotFound

- **3**: Rejected

- **4**: Pending

- **5**: UnknownError

- **6**: AccountRejected

### ModifyOrderErrorCode

0 = Success
1 = AccountNotFound
2 = OrderNotFound
3 = Rejected
4 = Pending
5 = UnknownError
6 = AccountRejected
7 = ContractNotFound

- **0**: Success

- **1**: AccountNotFound

- **2**: OrderNotFound

- **3**: Rejected

- **4**: Pending

- **5**: UnknownError

- **6**: AccountRejected

- **7**: ContractNotFound

### SearchPositionErrorCode

0 = Success
1 = AccountNotFound

- **0**: Success

- **1**: AccountNotFound

### ClosePositionErrorCode

0 = Success
1 = AccountNotFound
2 = PositionNotFound
3 = ContractNotFound
4 = ContractNotActive
5 = OrderRejected
6 = OrderPending
7 = UnknownError
8 = AccountRejected

- **0**: Success

- **1**: AccountNotFound

- **2**: PositionNotFound

- **3**: ContractNotFound

- **4**: ContractNotActive

- **5**: OrderRejected

- **6**: OrderPending

- **7**: UnknownError

- **8**: AccountRejected

### PartialClosePositionErrorCode

0 = Success
1 = AccountNotFound
2 = PositionNotFound
3 = ContractNotFound
4 = ContractNotActive
5 = InvalidCloseSize
6 = OrderRejected
7 = OrderPending
8 = UnknownError
9 = AccountRejected

- **0**: Success

- **1**: AccountNotFound

- **2**: PositionNotFound

- **3**: ContractNotFound

- **4**: ContractNotActive

- **5**: InvalidCloseSize

- **6**: OrderRejected

- **7**: OrderPending

- **8**: UnknownError

- **9**: AccountRejected

### SearchTradeErrorCode

0 = Success
1 = AccountNotFound

- **0**: Success

- **1**: AccountNotFound