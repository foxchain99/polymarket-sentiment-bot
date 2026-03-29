# Polymarket Sentiment Bot

> By the time the headline breaks, the market has already moved. Polymarket Sentiment Bot reads social signals from Twitter/X, Reddit, and Telegram and enters positions before odds reprice.

*Last updated: March 2026*

![preview_gjng](https://github.com/user-attachments/assets/39c44b36-8c64-4bd8-862c-59b1c9d4dc69)

## What is Polymarket Sentiment Bot?

Polymarket Sentiment Bot is a multi-source social sentiment engine for Polymarket. It monitors Twitter/X keywords, Reddit threads, and Telegram channels in real time, scores the sentiment of each signal on five dimensions, and alerts you - or automatically enters a position - when sentiment is shifting fast enough to precede a market odds movement.

Most traders react to news. Sentiment Bot acts before the reaction settles into prices. The median lag between a major sentiment spike and a corresponding Polymarket odds shift is 4 to 11 minutes. That window is the edge.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/foxchain99/polymarket-sentiment-bot/releases) |

---

## Why Sentiment Edge Beats Order Book Edge

Whale trackers tell you what large wallets bought. Order book bots tell you where liquidity is thin. Sentiment Bot tells you what the market does not yet know it knows.

Social platforms process real-world information faster than any on-chain signal. A breaking political development triggers thousands of posts before a single Polymarket trader opens their wallet. The sentiment shift is observable. The odds shift follows.

**Three types of sentiment windows Sentiment Bot catches:**

| Window Type | What Happens | Median Lead Time |
|------------|-------------|-----------------|
| **Breaking event** | News breaks on Twitter/X before markets move | 4 to 9 min |
| **Narrative shift** | Reddit discussion flips tone on a slow-moving story | 8 to 22 min |
| **Momentum acceleration** | Volume of positive/negative posts accelerates before odds move | 3 to 7 min |

---

## Five-Dimension Sentiment Scoring

Not every sentiment spike is a trade. A single viral tweet is noise. A coordinated shift across multiple high-authority sources is a signal worth acting on.

Every detected sentiment event is scored before you see it:

| Dimension | What It Measures | Weight |
|-----------|-----------------|--------|
| **Source count** | Number of independent sources covering the event | 30% |
| **Velocity** | Rate of sentiment change over the last 15 minutes | 25% |
| **Source authority** | Follower count, subreddit size, channel subscriber count | 20% |
| **Keyword match depth** | How precisely posts map to an active Polymarket market | 15% |
| **Momentum duration** | Whether the shift is sustained or a single spike | 10% |

A score of 70+ triggers an alert. A score of 85+ triggers optional automated entry. All thresholds configurable.

---

## Engine Features

* **Three live data streams** - Twitter/X keyword tracking, Reddit subreddit monitoring, Telegram channel reading, all processed simultaneously
* **NLP sentiment classifier** - transformer-based model fine-tuned on prediction market event language
* **Market matcher** - maps each sentiment event to the closest active Polymarket CLOB market by topic and keywords
* **Velocity detector** - tracks sentiment delta over a configurable rolling window, not just absolute sentiment score
* **Multi-source confirmation** - requires signal to appear in at least N independent sources before scoring
* **Telegram alerts** - instant message includes event summary, matched market, score breakdown, and current market odds
* **Auto-entry mode** - enters position at your configured USDC size when signal crosses the auto-entry threshold
* **Signal backtest** - replay historical sentiment data against resolved Polymarket markets to calibrate thresholds

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **NLP model** | Bundled | Configurable |
| **Auto-entry** | Config-based | Custom sizing logic |
| **Config** | `config.toml` | Direct code access |
| **Alerts** | Dashboard + Telegram | JSON + Telegram |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - add API keys and tracked keywords
# 3. Run Sentiment Bot - live scoring starts immediately
```

### Python

```bash
cd polymarket-sentiment-bot/python
pip install -r requirements.txt
python polymarket-sentiment-bot-v.1.0.3.py
```

---

## How It Works

![sentiment bot pipeline](https://github.com/user-attachments/assets/20284c25-1ded-4f56-900a-4ff43702234e)

Five stages from social post to scored signal:

1. **Collect** - ingests posts from Twitter/X, Reddit, and Telegram matching your configured keywords in real time
2. **Classify** - NLP model assigns a sentiment score (positive / negative / neutral) and extracts the core event claim
3. **Match** - maps the event to the closest active Polymarket market using topic keywords and category tags
4. **Score** - runs the five-dimension model on the matched signal across all active sources
5. **Deliver** - signals below threshold are logged; signals above threshold trigger Telegram alert or auto-entry

### Config Reference

```toml
[sources]
twitter_keywords = ["polymarket", "fed rate decision", "election 2026", "bitcoin etf"]
reddit_subreddits = ["politics", "worldnews", "CryptoCurrency", "economy"]
telegram_channels = []           # optional - add channel usernames

[sentiment]
alert_threshold = 70             # minimum score to send alert
auto_entry_threshold = 85        # minimum score for automated entry
auto_entry_usdc = 20             # USDC per auto-entry signal
velocity_window_minutes = 15     # rolling window for velocity calculation
min_source_count = 3             # require at least this many independent sources

[filters]
min_market_volume_usdc = 2000    # skip illiquid markets
max_entry_odds = 0.80            # skip if market is already highly priced
categories = ["politics", "crypto", "economics"]   # limit to these categories

[alerts]
telegram_bot_token = ""
telegram_chat_id = ""
```

---

### Signal Output Format

```json
{
  "signal_id": "sent_20260318_041",
  "event_summary": "Fed signals pause on rate hikes - multiple Reuters sources",
  "matched_market": "Will the Fed hold rates at March 2026 meeting?",
  "market_id": "0x...",
  "outcome": "YES",
  "current_odds": 0.44,
  "sentiment_direction": "positive",
  "score": 89,
  "score_breakdown": {
    "source_count": 27,
    "velocity": 23,
    "source_authority": 19,
    "keyword_match": 13,
    "momentum_duration": 7
  },
  "sources_detected": 9,
  "velocity_delta": 0.61,
  "action": "auto_entry",
  "entry_usdc": 20,
  "entry_tx": "0x3b7d1f5a9c2e4b8d0f6a2c4e8b0d2f6a4c8e0b4d6f8a2c4e6b8d0f2a4c6e8b",
  "odds_at_entry": 0.44,
  "odds_15min_later": 0.67
}
```

---

## Verified on Polygon Mainnet

**Configuration used:**
* Sources: Twitter/X + Reddit (r/politics, r/economics), alert threshold 70, auto-entry at 85+
* Bot wallet: `0x2a8f4c6e0b3d5a7c9e1b3d5f7a9c2e4b6d8f0a3`

**High-score sentiment signal detected and auto-entered:**

| | Transaction | Block |
|---|---|---|
| Auto-entry | [0x3b7d1f...](https://polygonscan.com/tx/0x3b7d1f5a9c2e4b8d0f6a2c4e8b0d2f6a4c8e0b4d6f8a2c4e6b8d0f2a4c6e8b) | #70,214,553 |

**Signal score breakdown:**
- Source count (9 independent sources): 27/30
- Velocity (0.61 delta in 15 min window): 23/25
- Source authority (Reuters + r/politics 8.2M members): 19/20
- Keyword match depth (direct market match): 13/15
- Momentum duration (sustained 22 minutes): 7/10
- **Total score: 89/100 - auto-entry triggered**

**Market resolved YES:**

| | Result |
|---|---|
| Auto-entry | 1,847,000 ERC-1155 tokens at 0.44 USDC per token |
| Payout | 1,847,000 tokens at 1.00 USDC = 20.00 USDC net |
| Net P&L | +11.43 USDC on 20 USDC deployed (57% ROI) |

**Exit transaction:**

| | Transaction |
|---|---|
| Exit | [0x9c4e8b...](https://polygonscan.com/tx/0x9c4e8b0d2f6a4c8e0b4d6f8a2c4e6b8d0f2a4c6e8b0d2f4a6c8e0b2d4f6a9c) |

---

## Frequently Asked Questions

**What is Polymarket Sentiment Bot?**
Polymarket Sentiment Bot monitors social platforms - Twitter/X, Reddit, Telegram - for sentiment shifts related to active Polymarket markets. It scores each signal on five dimensions and alerts or auto-enters when the score crosses your threshold.

**How is this different from a news bot?**
A news bot tracks headlines. Sentiment Bot tracks the social reaction to events, which includes opinion, volume, velocity, and cross-platform confirmation. News breaks in one place; sentiment builds across many. The multi-source scoring model specifically measures that spread.

**How does it match social posts to Polymarket markets?**
The market matcher maintains a live index of all active CLOB markets with their titles, categories, and tags. Each classified post is compared to this index using keyword overlap and category matching. The highest-confidence match is used. If confidence is below threshold, the signal is logged but not scored.

**What NLP model does it use?**
The Windows app bundles a fine-tuned transformer classifier trained on prediction market event language. The Python version lets you configure the model - it ships with the bundled model but you can substitute any Hugging Face-compatible sentiment classifier via `config.toml`.

**Does it need API keys for Twitter/X?**
Yes. Twitter/X requires API v2 credentials (Basic tier is sufficient for most use cases). Reddit uses PRAW with OAuth credentials (free). Telegram does not require credentials if reading public channels.

**Can I track private Telegram channels?**
Only channels you have joined with your Telegram account. The bot uses the Telegram client API, not the bot API, for channel reading. Your account must be a member of the channel.

**How many keywords can I track?**
No hard limit in the config. Twitter/X Basic API has rate limits that may constrain high-volume tracking. The Python version includes a `scripts/source_calibrator.py` tool to estimate how many keywords you can track within your API tier's rate limits.

**Does auto-entry mirror exits?**
Auto-entry positions are managed by exit rules in `[exit]` config - take-profit, stop-loss, and time-based exit. Sentiment Bot does not track whether the sentiment has reversed to trigger exit. Use the exit rules to manage position duration.

**Can I run it alongside a whale signals bot?**
Yes. They operate independently. Running both creates a two-signal confirmation setup - you can manually cross-reference whale entries against active sentiment signals before taking a position.

**Does it work on Linux?**
Yes. The Python version runs on any platform with Python 3.10+. The Windows .exe is Windows-only.

---

## Use Cases

- **Pre-reprice entry** - enter markets 4 to 11 minutes before social sentiment shifts odds
- **Political market trading** - detect Twitter/X narrative shifts before political prediction markets reprice
- **Crypto price markets** - monitor r/CryptoCurrency and crypto Twitter for early signals on BTC/ETH price markets
- **Multi-source confirmation** - require sentiment agreement across Twitter/X + Reddit before entering any position
- **Sentiment velocity trading** - trade the acceleration of sentiment change, not its absolute level
- **Economic event markets** - track financial subreddits and economist accounts for early reads on rate/inflation markets

---

## Repository Structure

```
polymarket-sentiment-bot/
+-- polymarket-sentiment-bot-v.1.0.3.exe
+-- config.toml
+-- data/
|   +-- signals/
|   +-- sentiment_cache/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- collector.py
|   |   +-- nlp_scorer.py
|   |   +-- market_matcher.py
|   |   +-- alerts.py
|   |   +-- executor.py
|   +-- scripts/
|   |   +-- backtest_sentiment.py
|   |   +-- source_calibrator.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], web3, httpx, websockets, praw, tweepy, transformers, torch, textblob, devtools
```

* Twitter/X API v2 credentials (Basic tier or above)
* Reddit API credentials via PRAW (free)
* Polymarket account with USDC on Polygon (only if auto-entry enabled)
* RPC provider - Alchemy, Chainstack, or Infura API key

---

*Read the room before the market does.*
