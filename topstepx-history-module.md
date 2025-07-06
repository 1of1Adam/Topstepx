---
title: "TopStepX History Module API Reference"
module_name: "History"
documentation_type: "ai_optimized_module"
file_size_kb: 27
parent_index: "topstepx-api-index.md"
base_url: "https://api.topstepx.com"
last_updated: "2025-07-06T19:56:31.754Z"
---

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

---

## 🔙 返回导航

- [📋 返回主索引](topstepx-api-index.md) - 查看所有模块
- [🌐 完整API概览](topstepx-api-index.md#api基础信息)

*此模块文档由Context7分块工具生成*