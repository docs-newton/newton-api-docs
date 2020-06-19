# Websocket connection

The Newton Pro API Websocket uses socket.io for Websocket requests.

This documentation explains how to connect properly through socket.io:

<!--
type: tab
title: NodeJS
-->

<!--
lineNumbers: true
-->

```javascript
const io = require("socket.io-client");

const socket = io.connect(
  `https://ws.newton.co/chart-data?symbol=${symbol}&candle=${candle}`,
  {
    transports: ["websocket"],
    reconnection: false,
  }
);

socket.emit("subscribe");

socket.on("initial", (data) => {
  //Initial logic
});

socket.on("update", (data) => {
  //Update Logic
});
```

<!-- type: tab-end -->

## Channels

### `SUB` /chart-data

Real-time chart data for a specified trading pair and timeframe

#### Parameters

| Property | Variable Type |                                          Values | Required |
| :------- | :-----------: | ----------------------------------------------: | :------: |
| symbol   |    String     |                                             Any |   true   |
| candle   |    String     | `"1m"`, `"5m"`, `"30m"`, `"1h"`, `"4h"`, `"1d"` |   true   |

#### Response Sample

##### `Initial` event

```json
[
  {
    "symbol": "BTC_CAD",
    "candle": "1h",
    "time": 1587110400,
    "open": 9940.3,
    "close": 9965.3,
    "high": 9996.25,
    "low": 9925
  },
  {
    "symbol": "BTC_CAD",
    "candle": "1h",
    "time": 1587114000,
    "open": 9967.34,
    "close": 10028.15,
    "high": 10064.25,
    "low": 9959.5
  }
]
```

##### `Update` event

```json
{
  "symbol": "BTC_CAD",
  "candle": "1h",
  "time": 1587110400,
  "open": 9940.3,
  "close": 9965.3,
  "high": 9996.25,
  "low": 9925
}
```

### `SUB` /order-book

Real-time order book for a specified trading pair

#### Parameters

| Property | Variable Type | Values | Required |
| :------- | :-----------: | -----: | -------: |
| symbol   |    String     |    Any |     true |

#### Response Sample

##### `Initial` event

```json
{
  "symbol": "BTC_CAD",
  "bids": [
    {
      "price": 7658.43,
      "quantity": 1,
      "actionType": "new"
    },
    {
      "price": 7657.43,
      "quantity": 1.5,
      "actionType": "new"
    }
  ],
  "asks": [
    {
      "price": 7659.43,
      "quantity": 1,
      "actionType": "new"
    },
    {
      "price": 7660.43,
      "quantity": 1.5,
      "actionType": "new"
    }
  ]
}
```

##### `Update` event

```json
{
  "symbol": "BTC_CAD",
  "bids": [
    {
      "price": 7658.43,
      "quantity": 1,
      "actionType": "new"
    },
    {
      "price": 7657.43,
      "quantity": 1.5,
      "actionType": "new"
    }
  ]
}
```

### `SUB` /trading-history

Real-time trading history for a specified trading pair

#### Parameters

| Property | Variable Type | Values | Required |
| :------- | :-----------: | -----: | -------: |
| symbol   |    String     |    Any |     true |

#### Response Sample

##### `Initial` event

```json
[
  {
    "orderId": "FDS34FW",
    "time": 1587686400,
    "symbol": "BTC_CAD",
    "price": 8765.34,
    "quantity": 432.43,
    "side": "bid"
  },
  {
    "orderId": "FDS34FW",
    "time": 1587686400,
    "symbol": "BTC_CAD",
    "price": 8765.34,
    "quantity": 432.43,
    "side": "bid"
  }
]
```

##### `Update` event

```json
{
  "orderId": "FDS34FW",
  "time": 1587686400,
  "symbol": "BTC_CAD",
  "price": 8765.34,
  "quantity": 432.43,
  "side": "bid"
}
```