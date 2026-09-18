# Tasks: Massive Data Core Client

## Phase 1: Exceptions Layer
- [ ] 1.1 Create `src/trading_firm/clients/massive/core/errors.py`.
- [ ] 1.2 Define `MassiveError` and its subclasses (`MassiveAuthError`, `MassiveRateLimitError`, `MassiveTransportError`).

## Phase 2: Transport Layer (TDD)
- [ ] 2.1 Create `tests/clients/massive/core/test_http.py`.
- [ ] 2.2 Write test: `httpClient` initializes with base URL and injects `Authorization: Bearer <key>` into the underlying session.
- [ ] 2.3 Write test: `httpClient.get()` raises `MassiveAuthError` on HTTP 401 without retries.
- [ ] 2.4 Write test: `httpClient.get()` retries up to 3 times on HTTT 429 and eventually raises `MassiveRateLimitError` (mocking `time.sleep`).
- [ ] 2.5 Write test: `httpClient.get()` succeeds if a retry returns HTTP 200.
- [ ] 2.6 Implement `src/trading_firm/clients/massive/core/http.py` to pass all tests.

## Phase 3: Facade Layer
- [ ] 3.1 Create `tests/clients/massive/core/test_client.py`.
- [ ] 3.2 Write test: `MassiveClient` initializes and successfully instantiates a private `_http: HttpClient` property.
- [ ] 3.3 Implement `src/trading_firm/clients/massive/core/client.py`.