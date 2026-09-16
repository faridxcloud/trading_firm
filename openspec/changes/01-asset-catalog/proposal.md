# Proposal: Asset Catalog (Security Master)

## Intent

Implement a standardized, immutable Asset Catalog (Security Master) that serves as the single source of truth for identifying, categorizing, and mapping tradeable financial instruments across global exchanges and data providers (e.g., Financial Modeling Prep, Polygon, CCXT).

## Scope

### In Scope
- **Core Identity & Taxonomy:** Modeling assets with canonical symbol, name, asset class (Stock, ETF, Crypto, Forex), domestic exchange (NYSE, NASDAQ, BME, etc.), and jurisdiction/country.
- **Operational & Trading Rules:** Capturing currency/quote currency, minimum tick size (e.g., 0.01), minimum order quantity / lot size (e.g., 1.0), and canonical market timezone (e.g., America/New_York, UTC).
- **Research Metadata:** Industry sector and asset lifecycle status (is_active).
- **Symbology Mapping:** Translating internal canonical symbols to external data vendor tickers (e.g., internal TSLA -> FMP TSLA, Binance BTCUSDT).
- **In-Memory Catalog Registry:** Fast lookups by symbol, exchange, or sector with strict schema validation.

### Out of Scope
- Downloading historical price series or live market data (owned by 02-market-data-extractor).
- Persistent SQL/NoSQL database backends (initial version uses structured file / in-memory catalog).
- Broker order routing and execution logic.

## Capabilities

### New Capabilities
- asset-catalog: Domain entity Asset, vendor symbology dictionary, and AssetCatalog repository with search and validation methods.

### Modified Capabilities
None (Initial baseline component).

## Approach

1. Define a strongly typed, frozen/immutable domain entity Asset in Python with input validation (rejection of non-positive tick sizes or invalid timezones).
2. Implement an AssetCatalog repository with query methods (get, filter_by_exchange, filter_by_sector).
3. Include vendor symbology resolver to isolate external API naming idiosyncrasies from the core pipeline.
4. Establish comprehensive unit test coverage verifying immutability, validation constraints, and lookup performance.

## Affected Areas

| Component | Path | Impact |
|---|---|---|
| Domain Models | src/trading_firm/catalog/models.py | New immutable Asset and Symbology types |
| Repository | src/trading_firm/catalog/repository.py | In-memory query engine and registry |
| Tests | tests/catalog/test_models.py, tests/catalog/test_repository.py | Full validation test suite |
