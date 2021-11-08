# Endpoints

All Boltrswap pairs consist of two different tokens. ETH is not a native currency in Boltrswap, and is represented only by WMATIC in the pairs. 

The canonical WMATIC address used by the Boltrswap interface is `0x0d500b1d8e8ef31e21c99d1db9a6444d3adf1270`.

Results are edge-cached for 1 minute (or 60 seconds) and refreshed in background (`stale-while-revalidate`).

## [`/summary`](https://api.boltrswap.com/api/summary)

Returns data for the top ~1000 Boltrswap pairs, sorted by reserves. 

### Request

`GET https://api.boltrswap.com/api/summary`

### Response

```json5
{
  "0x..._0x...": {                  // the asset ids of the ERC20 tokens (i.e. token addresses), joined by an underscore
    "last_price": 1.234,            // denominated in token0/token1
    "base_volume": 123.456,         // last 24h volume denominated in token0
    "quote_volume": 1234.56,        // last 24h volume denominated in token1
    "liquidity": 1234.56,           // liquidity denominated in USD
    "liquidity_ETH": 1234.56        // liquidity denominated in ETH
  },
  // ...
}
```

## [`/assets`](https://api.boltrswap.com/api/assets)

Returns the tokens in the top ~1000 pairs on Boltrswap, sorted by reserves. 

### Request

`GET https://api.boltrswap.com/api/assets`

### Response

```json5
{
  // ...,
  "0x...": {              // the address of the ERC20 token
    "name": "...",        // not necessarily included for ERC20 tokens
    "symbol": "...",      // not necessarily included for ERC20 tokens
    "maker_fee": 0,       // always 0
    "taker_fee": 0.002,   // always 0.002 i.e. .2%
  },
  // ...
}
```

## [`/tickers`](https://api.boltrswap.com/api/tickers)

Returns data for the top ~1000 Boltrswap pairs, sorted by reserves.

### Request

`GET https://api.boltrswap.com/api/tickers`

### Response

```json5
{
  "0x..._0x...": {                  // the asset ids of ETH and ERC20 tokens, joined by an underscore
    "base_name": "...",             // token0 name
    "base_symbol": "...",           // token0 symbol
    "base_address": "0x...",        // token0 address
    "base_id": "0x...",             // token0 address
    "quote_name": "...",            // token1 name
    "quote_symbol": "...",          // token1 symbol
    "quote_address": "0x...",       // token1 address
    "quote_id": "0x...",            // token1 address
    "last_price": 1.234,            // the mid price as token1/token0
    "base_volume": 123.456,         // denominated in token0
    "quote_volume": 1234.56,        // denominated in token1
    "liquidity": 1234.56,           // liquidity denominated in USD
    "liquidity_ETH": 1234.56        // liquidity denominated in ETH
  },
  // ...
}
```

## `/orderbook/:pair`

Returns simulated orderbook data for the given Boltrswap pair.
Since Boltrswap has a continuous orderbook, fixed amounts in an interval are chosen for bids and asks, 
and prices are derived from the Boltrswap formula (accounting for both slippage and fees paid to LPs). 

### Request

`GET https://api.boltrswap.com/api/orderbook/:pair`

### URL Parameters

- `pair`: The asset ids of two ERC20 tokens, joined by an underscore, e.g. `0x..._0x...`. The first token address is considered the base in the response.

### Response

```json5
{
  "updated_at": 1234567, // UNIX timestamp of the response
  "bids": [
    [12, 1.2],           // denominated in base token, quote token/base token
    [12, 1.1],           // denominated in base token, quote token/base token
    // ...
  ],
  "asks": [
    [12, 1.3],           // denominated in base token, quote token/base token
    [12, 1.4],           // denominated in base token, quote token/base token
    // ...
  ]
}
```

## `/trades/:pair`

Returns all swaps in the last 24 hours for the given Boltrswap pair. 

The pair address is the address of the two tokens in either order.
The first address is considered the base in the response.

### URL Parameters

- `pair`: The asset ids of two ERC20 tokens, joined by an underscore, e.g. `0x..._0x...`. The first token address is considered the base in the response.

### Request

`GET https://api.boltrswap.com/api/trades/:pair`

### Response

```json5
[
  {
    "trade_id": "...",
    "price": "1.234",           // denominated in quote token/base token
    "base_volume": "123.456",   // denominated in base token
    "quote_volume": "1234.56",  // denominated in quote token
    "trade_timestamp": 1234567, // UNIX timestamp
    "type": "buy"               // "buy"/"sell"/"borrow-both"/"???" 
  },
  // ...
]
```
