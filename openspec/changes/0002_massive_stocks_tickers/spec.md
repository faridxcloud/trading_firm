# Specification: Massive Data Stocks Tickers Client

## Purpose

Define the behavioral requirements, schema contracts, and test scenarios for the `MassiveStocksTickers.all_tickers()` integration against Massive's `/v3/reference/tickers` endpoint.

## Requirements

### Requirement: Configurable Query Parameters with Production Defaults

The `MassiveStocksTickers.all_tickers()` method MUST provide sensible production defaults while allowing callers to override filter parameters:
- `market`: String enum (`stocks`, `crypto`, `fx`, `otc`, `indices`), default MUST be `"stocks"`.
- `active`: Boolean flag, default MUST be `True`.
- `limit`: Integer batch size, default MUST be `1000` (the maximum supported page size).
- `order`: Sort order (`"asc"` or `"desc"`), default MUST be `"asc"`.
- `sort`: Field sorting, default MUST be `"ticker"`.

#### Scenario: Default invocation
- GIVEN a configured `MassiveStocksTickers` client
- WHEN `all_tickers()` is called without arguments
- THEN the initial request query string MUST contain `market=stocks&active=true&limit=1000&order=asc&sort=ticker`

#### Scenario: Custom market and active filters
- GIVEN a configured `MassiveStocksTickers` client
- WHEN `all_tickers(market="crypto", active=False)` is called
- THEN the initial request query string MUST contain `market=crypto&active=false`

---

### Requirement: Automated Cursor-based Pagination via `next_url`

The client MUST handle Massive's cursor pagination automatically and transparently:
1. The client MUST inspect the top-level `next_url` attribute in every JSON response.
2. If `next_url` is non-empty, the client MUST follow that URL to retrieve the subsequent batch.
3. The client MUST inject the `Authorization: Bearer <apiKey>` header into every `next_url` request to prevent HTTP 401 errors.
4. When `next_url` is null, omitted, or empty, the client MUST gracefully terminate pagination.
5. The method MUST support lazy evaluation (Python generator yielding `TickerRecord` items one by one).

#### Scenario: Multi-page cursor traversal
- GIVEN a query returning multiple pages where Page 1 includes `next_url = "https://api.massive.com/v3/reference/tickers?cursor=XYZ"`
- AND Page 2 returns `next_url = None`
- WHEN iterating over `all_tickers()`
- THEN the klient MUST execute exactly 2 HTTP requests
- AND the second request MUST target `https://api.massive.com/v3/reference/tickers?cursor=XYZ` with the Bearer authorization header
- AND all ticker records from both pages MUST be yielded in continuous sequence

#### Scenario: Single page termination
- GIVEN an API response where `next_url` is null
- WHEN iterating over `all_tickers()`
- THEN exactly 1 HTTP request MUST be made and iteration MUST complete without errors

#### Scenario: Empty results
- GIVEN an API response with `count = 0` and `results = []`
- WHEN iterating over `all_tickers()`
- THEN 0 items MUST be yielded and no subsequent requests MUST occur

---

### Requirement: Strongly Typed Result Mapping (`TickerRecord`)

Every raw JSON object in `results[]` MUST be deserialized into an immutable `TickerRecord` dataclass adhering to Massive's documented schema:
- **Mandatory fields:** `ticker` (str), `name` (str), `market` (str), `locale` (str), `active` (bool).
- **Microstructure fields:** `primary_exchange` (str, ISO MIC code), `type` (str), `currency_name` (str).
- **Nullable / Optional identifiers:**
  - `cik`: Optional[str] (SEC CIK)
  - `composite_figi`: Optional[str] (OpenFIGI composite)
  - `share_class_figi`: Optional[str] (OpenFIGI share class)
  - `last_updated_utc`: Optional[str]
  - `delisted_utc`: Optional[str]
  - `currency_symbol`: Optional[str]
  - `base_currency_name`: Optional[str]
  - `base_currency_symbol`: Optional[str]

#### Scenario: Standard equity record deserialization
- GIVEN a JSON result with `ticker: "AAPL", primary_exchange: "XNAS", type: "CS", active: true`
- WHEN the record is parsed into `TickerRecord`
- THEN all fields MUST match their respective types and the object MUST be frozen/immutable

#### Scenario: Missing optional fields
- GIVEN an OTC or international ticker missing `cik` and `composite_figi` (omitted or null in JSON)
- WHEN the record is parsed into `TickerRecord`
- THEN `record.cik` and `record.composite_figi` MUST default to `None` without raising `KeyError`

---

### Requirement: Network Transport Resiliency and Error Handling

The client MUST provide enterprise-grade reliability over the network:
1. **Rate Limiting:** On receiving `HTTP 429 (Too Many Requests)`, the transport MUST back off exponentially and retry up to 3 times before failing.
2. **Authentication Failure:** On `HTTP 401 (Unauthorized)`, the client MUST immediately raise a specific `MassiveAuthError` without retrying.
3. **Server Errors:** On `HTTP 500/502/503/504`, the client MUST retry with backoff.
4. **Network Timeouts:** Requests MUST enforce a configurable timeout (default: 15.0 seconds).

#### Scenario: HTTP 429 Rate limit backoff and recovery
- GIVEN a request that receives `HTTP 429` on initial attempt
- AND succeeds with `HTTP 200` on the second attempt
- WHEN `all_tickers()` is executed
- THEN the client MUST sleep according to retry backoff and successfully return the data

#### Scenario: Fatal authentication failure
- GIVEN an invalid or expired API key resulting in `HTTP 401`
- WHEN `all_tickers()` is called
- THEN the client MUST immediately raise `MassiveAuthError` with an informative error message
