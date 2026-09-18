# Proposal: Massive Data Stocks Tickers Client

## Intent

Implement the foundational Massive Data API integration layer, delivering the core HTTP client infrastructure, the MassiveClient facade, and the MassiveStocksTickers sub-service to consume the /v3/reference/tickers endpoint (all_tickers) with transparent cursor-based pagination.

## Scope

### In Scope
- Resilient HTTP Transport: Base HTTP client managing authentication (Bearer token via Authorization headers), configurable timeouts, connection pooling, and automatic retry on HTTP 429 and 5xx.
- Unified Client Facade: Top-level MassiveClient entry point composing specialized sub-clients to mirror Massive official documentation structure.
- Stocks Tickers Sub-Service (MassiveStocksTickers): all_tickers() method supporting query parameters: market, active, limit, sort, order.
- Cursor Pagination Engine: Consuming next_url transparently without exposing raw cursor tokens.
- Typed Response Models: Strongly typed dataclasses for ticker records.
- Comprehensive Unit Testing: Mocked HTTP transports validating pagination boundaries and error handling.

### Out of Scope
- Historical aggregate bars (OHLCV) (reserved for 0002-massive-stocks-aggregates).
- Non-stock asset classes.
- Local database caching or Parquet persistence.

## Capabilities

### New Capabilities
- massive-stocks-tickers: Core HTTP client, MassiveClient facade, and MassiveStocksTickers.all_tickers() with cursor pagination and typed schema models.

### Modified Capabilities
None.

## Approach

1. HTTP Layer: Build src/trading_firm/clients/massive/http.py.
2. Schema Models: Define TickerRecord and TickersPageResponse in src/trading_firm/clients/massive/models.py.
3. Tickers Service: Implement MassiveStocksTickers in src/trading_firm/clients/massive/stocks/tickers.py.
4. Facade Assembly: Expose MassiveClient in src/trading_firm/clients/massive/client.py.
5. Testing: Write unit tests in tests/clients/massive/test_tickers.py.

## Affected Areas

| Component | Path | Impact |
|---|---|---|
| HTTP Transport | src/trading_firm/clients/massive/http.py | Base authenticated network client |
| Models | src/trading_firm/clients/massive/models.py | Typed dataclasses for ticker records |
| Tickers Sub-service | src/trading_firm/clients/massive/stocks/tickers.py | Endpoint consumer & cursor pagination |
| Client Facade | src/trading_firm/clients/massive/client.py | Main entry point |
| Tests | tests/clients/massive/test_tickers.py | Mocked transport & pagination test suite |
