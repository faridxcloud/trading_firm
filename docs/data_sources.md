# External Market Data Sources & Financial APIs

Technical reference and catalog of financial data providers evaluated for the Quantitative Trading Firm.

---

## 1. Global Multi-Asset & Fundamental Providers

### [Financial Modeling Prep (FMP)](https://site.financialmodelingprep.com/)
- **Coverage:** Global (50+ stock exchanges across North America, Europe, Asia, and Latin America).
- **Core Strengths:** Security master catalog, detailed financial statements (10-K, 10-Q), valuation metrics (P/E, EV/EBITDA), earnings surprises, and historical EOD/intraday prices.
- **Project Role:** Primary source for the global Asset Catalog and fundamental feature extraction.

### [EODHD (EOD Historical Data)](https://eodhd.com/)
- **Coverage:** Ultra-broad global coverage (70+ exchanges, 150,000+ tickers).
- **Core Strengths:** Clean End-of-Day (EOD) data, corporate actions (splits/dividends), international fundamentals, and macro indices in developed and emerging markets.
- **Project Role:** Cross-validation and secondary backup for international non-US equities.

### [Finnhub](https://finnhub.io/)
- **Coverage:** Global (US, Europe, Asia, Latin America).
- **Core Strengths:** Balanced suite offering real-time streaming (REST and WebSockets), fundamental filings, news sentiment scoring (NLP), insider transactions, and institutional ownership.
- **Project Role:** Market sentiment features and real-time international quotes.

### [Twelve Data](https://twelvedata.com/)
- **Coverage:** Global (Equities, Forex, Indices, Crypto, ETFs).
- **Core Strengths:** Developer-friendly modern API with 100+ precalculated technical indicators directly accessible via REST and low-latency WebSockets.
- **Project Role:** Rapid technical factor validation, Forex, and benchmark ETF feeds.

### [Massive Data](https://massive.com/)
- **Coverage:** Multi-asset global data feeds.
- **Core Strengths:** Large-scale market data aggregation, alternative data sets, and institutional feeds.
- **Project Role:** Specialized data enrichment and alternative market signals.

---

## 2. US Equities, Options & Real-Time Streaming

### [Polygon.io](https://polygon.io/)
- **Coverage:** US Markets (NYSE, NASDAQ, AMEX, Cboe, US Options, Indices, Crypto).
- **Core Strengths:** Institutional-grade data quality, 100% tick-level, second-level, and minute-level historical data aggregates, ultra-fast streaming WebSockets.
- **Project Role:** Primary source for high-resolution intraday backtesting and real-time tick streaming for US equities.

### [Tiingo](https://www.tiingo.com/)
- **Coverage:** US Equities, Mutual Funds, ETFs, and Chinese ADRs.
- **Core Strengths:** Renowned for flawless split and dividend price adjustments (eliminating historical survivorship and corporate action distortions), plus curated news sentiment.
- **Project Role:** Long-term clean historical price series without split anomalies.

---

## 3. Order Book Microstructure & High-Frequency Data (Level 2 / Level 3 MBO)

### [Databento](https://databento.com/)
- **Coverage:** US Venues (CME Group: CME, CBOT, NYMEX, COMEX; NASDAQ; Cboe; ICE; MIAX).
- **Core Strengths:** Nanosecond-resolution Market-By-Order (Level 3 MBO) and Market-By-Price (Level 2 MBP) tick feeds. Proprietary zero-copy binary format (DBN) with pay-as-you-go self-service pricing.
- **Project Role:** Order flow imbalance, microstructure feature engineering, and high-precision execution research.

### [Tardis.dev](https://tardis.dev/)
- **Coverage:** Cryptocurrency Derivatives & Spot (Binance, Bybit, Coinbase, OKX, Kraken, etc.).
- **Core Strengths:** The crypto counterpart to Databento. Continuous recording of raw WebSocket delta feeds with Python client library to deterministically replay and reconstruct historical order books.
- **Project Role:** Crypto order book depth modeling and market making research.

---

## 4. Execution & Brokerage APIs (Paper & Live Trading)

### [Alpaca Markets](https://alpaca.markets/)
- **Coverage:** US Equities and Cryptocurrencies.
- **Core Strengths:** Developer-first commission-free broker API. Completely free Paper Trading environment mirroring real live trading execution semantics.
- **Project Role:** Automated order execution, position tracking, and paper trading validation.

### [Interactive Brokers (IBKR API)](https://www.interactivebrokers.com/)
- **Coverage:** 150+ electronic market centers in 34 countries (Equities, Futures, Options, Bonds, Currencies).
- **Core Strengths:** The undisputed institutional multi-asset broker standard for algorithmic execution worldwide.
- **Project Role:** Global multi-currency order execution and production deployment.

---

## 5. Specialized, Alternative & Free Data Feeds

### [SEC EDGAR API](https://www.sec.gov/edgar/sec-api-documentation)
- **Coverage:** All US publicly traded companies.
- **Core Strengths:** 100% official, free JSON endpoints to raw SEC corporate filings (10-K, 10-Q, 8-K, Form 4 insider trades).
- **Project Role:** Direct audited fundamental ingestion without third-party vendor risk.

### [QuickFS](https://quickfs.net/)
- **Coverage:** 40+ global exchanges across 30+ countries.
- **Core Strengths:** Unbroken 10-to-20 year continuous standardized fundamental accounting data.
- **Project Role:** Long-term value-factor investing and deep financial ratio research.

### [CCXT (Crypto Currency eXchange Trading)](https://ccxt.com/)
- **Coverage:** Over 100 cryptocurrency exchanges in a single unified Python framework.
- **Core Strengths:** Normalized API layer abstracting exchange-specific quirks.
- **Project Role:** Universal connector for crypto multi-exchange liquidity and arbitrage.
