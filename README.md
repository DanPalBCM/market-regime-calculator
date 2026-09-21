# Market Regime Calculator

**Version 1.0 — calibrated September 2026**

A rules-based framework for classifying the US equity market (S&P 500) into one of three regimes: **Bull**, **Flat**, or **Bear**.

---

## 0. Instructions for the model running this

You are being asked to assess market conditions using the framework below. Follow this procedure exactly.

1. Look up the current value of every signal in Section 2 using web search. Use the named sources where possible.
2. Record the value, its date, and the source for each.
3. Score each signal `+1`, `0`, or `-1` using the stated thresholds.
4. Check every override in Section 4. An override fires regardless of the total score.
5. Sum the scores and classify the regime using Section 5.
6. If signals are split or the total sits on a boundary, consult the tiebreakers in Section 6.
7. Produce output in the exact format given in Section 7.

**Rules you must follow:**

- **Do not substitute judgment for the thresholds.** If a value is neutral by the rule, score it neutral even if the narrative feels bearish. The framework exists to override narrative.
- **State staleness.** If the most recent data you can find is more than 10 days old for a daily/weekly signal, say so explicitly.
- **Never fabricate a value.** If you cannot find a signal, mark it `UNAVAILABLE`, score it `0`, and note that the total is less reliable.
- **Report disconfirming evidence.** If the score contradicts the user's stated thesis, say so plainly. That is the primary purpose of this tool.
- This is an analytical framework, not financial advice.

---

## 1. Design logic

The framework tracks two components of price and one regime discriminator.

| Component | Signals | What it determines |
|---|---|---|
| **P** (the multiple) | 10-year Treasury, Brent crude, diesel crack | What investors will pay per dollar of earnings |
| **E** (earnings) | AI capex | Whether the earnings engine holds |
| **Regime** | High-yield credit spreads | Whether a decline is a 10% grind or a 25% break |

Two principles govern every signal:

- **Levels tell you the regime. Rates of change tell you the turn.** Most people watch levels and miss turns.
- **Direction alone is not a signal — you must know the cause.** Falling yields can be bullish (disinflation) or bearish (recession pricing). Falling diesel cracks can be bullish (supply returns) or bearish (freight demand collapsed). Always identify which.

---

## 2. Core signals

Five signals, each weighted **1.0**. Score range: **-5 to +5**.

---

### Signal A — 10-Year Treasury Yield

**Why:** The discount rate for equities. This, not the fed funds rate, sets the multiple.

**Where to find it:** FRED series `DGS10`; US Treasury daily yield curve; CNBC quote `US10Y`.

| Score | Condition |
|---|---|
| **+1** | Below 4.50% |
| **0** | 4.50% – 5.15% |
| **-1** | Above 5.15% for 10 consecutive sessions, **OR** any 75bp rise within 90 days at any level, **OR** yields falling with the 2-year dropping faster than the 10-year (bull steepening) |

**Notes:**
- Speed beats level. A 100bp rise in three months breaks something regardless of the starting point.
- Also check the real yield (FRED `DFII10`). Above ~2.5% is genuinely restrictive.
- Check the curve shape (FRED `T10Y2Y`). Bull steepening = recession being priced = bearish, even though yields are falling.

**Valuation cross-check:** Compute the equity risk premium as `(1 / forward P/E) - 10Y yield`. Post-2000 average is roughly 3%. A reading near zero means stocks carry no cushion and are priced entirely on earnings growth. Report this number; it does not score.

---

### Signal B — Brent Crude

**Why:** A supply-driven oil increase is a tax on consumers and a direct input to headline inflation.

**Where to find it:** EIA spot prices; Trading Economics; oilprice.com.

| Score | Condition |
|---|---|
| **+1** | Below $85 for four consecutive weeks |
| **0** | $85 – $120 |
| **-1** | Above $120, **OR** a 25% rise within 90 days |

**Why $120:** Oil shocks reliably predict recessions only when the price exceeds its **trailing three-year maximum**. Moves inside an established range are noise the economy has already absorbed. The trailing three-year max as of late 2026 is approximately $120 (set April 2026).

**A model running this later must recompute the trailing three-year maximum and reset this threshold accordingly.**

**Also determine:** Is the move supply-driven or demand-driven? Demand-driven increases signal growth and are **bullish**. Supply-driven increases are a tax and are **bearish**. Only supply-driven moves score negative.

---

### Signal C — Diesel Crack Spread

**Why:** Diesel is a production input — freight, rail, agriculture, construction, shipping. Unlike crude, it passes into **core** inflation via delivered goods cost, which is the part central banks cannot look through.

**Use the crack spread, not the diesel price.** Diesel price and crude are ~90% correlated; using the price double-counts Signal B. The crack (diesel minus crude) is orthogonal by construction and isolates refining scarcity as a separate shock.

**Where to find it:** EIA "Today in Energy" and Short-Term Energy Outlook; RBN Energy; NY Harbor ULSD vs WTI or Brent. Normal range with balanced supply is **$20–30/bbl**.

| Score | Condition |
|---|---|
| **+1** | Below $35 **and** at least two physical confirmations (see below) |
| **0** | $35 – $50 |
| **-1** | Above $50, **OR** a 30% rise within 30 days |

**Asymmetric confirmation rule — important.**

The crack is a *price*, and prices collapse on announcements while physical shortages persist. A ceasefire headline can compress the crack 30–40% in days while $6/gal retail diesel and freight surcharges keep feeding core CPI for another year.

Therefore: **bearish moves score immediately; bullish moves require physical confirmation.** At least two of:

1. Three consecutive weekly distillate inventory builds toward the five-year average
2. Refinery utilization falling below 95% (slack returning)
3. Retail diesel down more than 15% from its recent high

**Physical confirming data (unscored, EIA Weekly Petroleum Status Report, Wednesdays):**
- Distillate inventories vs 5-year seasonal average — bearish if >10% below
- Refinery utilization — above 95% means high margins cannot call forth more supply, so price does all the adjusting
- Gasoil/ULSD timespread backwardation — steep backwardation confirms physical scarcity rather than speculation

**Ask which side rebalanced.** Cracks can normalize because supply returned *or* because freight demand collapsed. The second is a recession signal wearing a bull costume.

**Restart lag.** Refinery repair is not instantaneous. Tanks and control rooms take weeks; distillation columns take months; hydrocrackers and hydrotreaters — the units that actually make diesel — are high-pressure forged vessels with 12–24 month lead times from a handful of global suppliers. Inventory rebuild takes longer still, since it requires producing above demand, which is impossible at 97% utilization. Do not treat a peace headline as a supply event.

---

### Signal D — High-Yield Credit Spreads

**Why:** The most reliable real-time recession probability available. Credit sees trouble before equities do.

**Where to find it:** FRED series `BAMLH0A0HYM2` (HY OAS), `BAMLH0A3HYC` (CCC), `BAMLC0A0CM` (IG).

| Score | Condition |
|---|---|
| **+1** | Below 300bp and stable or tightening |
| **0** | 300 – 400bp |
| **-1** | Above 400bp, **OR** +100bp widening within 30 days, **OR** CCC spreads widening while BB stays flat |

**Reference levels:** <300 complacent · 300–400 normal · 400–500 warning · 500–700 recession priced · >800 crisis (2008 peaked near 2,000bp; 2020 near 1,100bp).

**Notes:**
- The *change* outranks the level. 100bp of widening in a month is the signal regardless of where it started.
- Watch the CCC–BB gap. The low-quality tail always widens first. CCCs blowing out while BBs stay calm is the early crack; both moving together is systemic.
- **The most dangerous configuration is spreads widening while equities are still near highs.** That divergence has preceded most real drawdowns.

---

### Signal E — AI Capital Expenditure

**Why:** Concentrated hyperscaler capex is the current earnings engine. This is the `E` in the P/E.

**Where to find it:** Quarterly 10-Q filings and earnings calls for Microsoft, Amazon, Alphabet, Meta. Assess once per quarter.

| Score | Condition |
|---|---|
| **+1** | Aggregate guidance raised, funded from operating cash flow |
| **0** | Guidance maintained, **or** rising but with growing debt/SPV/securitization funding |
| **-1** | Any major hyperscaler cuts guidance or uses the word "digesting", **OR** GPU depreciation schedules extended |

**Quality signals to check (affect severity, not probability):**
- **Funding mix.** Capex from operating cash flow is sustainable indefinitely. Capex funded by debt, SPVs, or securitization is reflexive and breaks. This is the 1999 telecom vendor-financing pattern.
- **Depreciation schedules.** Extending GPU useful life is earnings management and a loud tell.
- **Circular financing.** Vendors taking equity/warrants in customers, GPU-backed securitization, supplier warrants.
- **The gap** between capex disclosed and AI revenue disclosed.

These do not raise the probability of a bear market. They raise its **depth**.

---

## 3. Deprioritized: payroll data

Monthly nonfarm payrolls carry a confidence interval of roughly ±100k, meaning a 50k "beat" is statistically indistinguishable from zero, and revisions routinely exceed the original signal. **Do not score payrolls.**

Substitute instruments that are timely and barely revised:
- **Weekly initial claims** (DOL, Thursdays) — four-day lag, minimal revision
- **Unemployment rate** (BLS, household survey) — not revised

Both appear in the tiebreakers below.

---

## 4. Overrides

Any single override fires regardless of the total score.

**To Bear:**
- HY OAS above 500bp — *credit is dispositive; it leads equities at real turns*
- Brent above $130
- 10-year above 5.50%
- Two or more hyperscalers cutting capex in the same quarter
- **Transmission override:** core goods CPI rises for two consecutive months (the freight pass-through arriving)
- **Transmission override:** the Fed's forward dot plot median moves up by 25bp or more versus the prior meeting

**To Bull:**
- A verified supply resolution — e.g. a chokepoint reopening with transit counts actually recovering — **and** Brent below $85. This collapses the oil → CPI → Fed chain at its source.

The two transmission overrides exist because an energy shock only matters to equities if it reaches the macro. Without transmission, a record crack spread is a sector story. With it, it becomes a Fed story, and Fed stories compress multiples.

---

## 5. Aggregation

Sum the five signals.

| Total | Regime |
|---|---|
| **+2 or higher** | **Bull** |
| **-1 to +1** | **Flat** |
| **-2 or lower** | **Bear** |

**Whipsaw guard:** a regime change requires either two consecutive weekly readings at the new level, or a single override firing. Without this you will flip every time oil moves 4%.

**Cadence:** check Signals A, B, C, D weekly. Check Signal E quarterly, at earnings. Re-run the full assessment monthly at minimum.

---

## 6. Tiebreakers

Unscored. Use only when the core signals are split or the total sits on a boundary.

| Indicator | Bull | Bear |
|---|---|---|
| Initial claims, 4-week avg | Below 230k | Above 260k sustained |
| Unemployment rate | Stable | 0.4pp above its 12-month low |
| Core PCE, y/y | Below 3.0% and falling | Above 3.6% |
| % of S&P above 200-day MA | Above 60% | Below 40% |
| Refiner vs driller equity spread | Narrowing (squeeze ending) | Widening |
| Price confirmation | Break above prior all-time high | Break below prior swing low |

The refiner/driller spread is a useful early read: it typically signals the end of a refining squeeze before inventory data confirms it.

---

## 7. Required output format

```
MARKET REGIME ASSESSMENT — [date]

SIGNALS
A. 10-Year Treasury    [value] ([date])  → [+1/0/-1]  [one-line reason]
B. Brent Crude         [value] ([date])  → [+1/0/-1]  [one-line reason]
C. Diesel Crack        [value] ([date])  → [+1/0/-1]  [one-line reason]
D. HY OAS              [value] ([date])  → [+1/0/-1]  [one-line reason]
E. AI Capex            [value] ([date])  → [+1/0/-1]  [one-line reason]

TOTAL: [sum]
OVERRIDES FIRED: [list, or "none"]
REGIME: [Bull / Flat / Bear]

CONTEXT
Equity risk premium: [value]
Curve shape (2s10s): [value, direction]
Data staleness: [any signal >10 days old]

DIVERGENCES
[Any signal materially disagreeing with the others, and what it implies]

WHAT WOULD CHANGE THIS
[2-3 specific, measurable conditions that would flip the regime]
```

---

## 8. Worked example — September 2026

For calibration. A model running this later should compare current readings against this baseline.

| Signal | Reading | Score |
|---|---|---|
| A. 10-Year | ~4.95% (highest since 2007) | **0** |
| B. Brent | ~$97–104 | **0** |
| C. Diesel crack | ~$107/bbl (all-time record) | **-1** |
| D. HY OAS | ~270bp | **+1** |
| E. AI capex | Q2 guidance raised, ~$220bn Amazon 2026 capex | **+1** |

**Total: +1 → Flat.** No overrides fired.

**Context:** Fed funds 3.75–4.00% after a September hike, with the dot plot implying one more. Core PCE 3.3%. Forward P/E ~20, giving an equity risk premium near zero. S&P roughly 2.5% below its August high.

**The key divergence:** an unprecedented energy shock — diesel cracks at a record against a normal range of $20–30, distillate inventories at multi-decade lows, roughly 1.5 mb/d of diesel supply off the market from combined Middle East and Russian disruption — coexisting with high-yield spreads at 270bp showing zero stress.

**Interpretation:** either credit is right and demand destruction absorbs a sector-specific squeeze, or credit is late. Credit being late is historically how these things go. This divergence, not the absolute level of any single signal, is the thing to monitor.

---

## 9. Known limitations

- **Thresholds are judgment-calibrated to the 2026 regime**, not statistically optimized. They will need recalibration if the inflation or rate environment changes materially.
- **The framework is slow at tops.** It is built to avoid false bear signals, which means it will register a genuine top late. That trade-off is deliberate.
- **Signal E is quarterly**, so the earnings component of the score is stale for most of any given quarter.
- **No liquidity or positioning input.** Sharp mechanical moves driven by systematic funds, vol-targeting, or short squeezes will not appear in these signals at all. Such moves are usually noise, but not always.
- **Five signals is a small sample.** A single miscalibrated threshold moves the regime by a full category.

---

## 10. Epistemic guidance for the model

**Confirmation is worthless.** If the user holds a directional thesis, they can find supporting evidence in any dataset. The value of this framework is entirely in its capacity to disagree with them.

State plainly when the signals do not support the user's stated view. Do not soften it. Note specifically when a bearish narrative — high rates, geopolitical conflict, central bank fallibility — is not ratified by measurable signals. A narrative may be early rather than wrong, but **"early" and "wrong" are indistinguishable in real time**, which is the entire reason to use rules.

Ask the user to define their **surrender conditions** in advance: the specific, measurable readings that would force them to abandon their thesis. Then hold them to it.

**Central banks and markets are both poor at forecasting inflection points.** Neither the dot plot nor rate futures should be treated as information about the future. Build assessments that do not require any rate path to be correct, and remember that forecast error is symmetric — a dovish surprise is as likely as a hawkish one.

---

*This is an analytical framework for understanding market conditions. It is not financial advice, and no threshold here should be treated as a trading rule.*
