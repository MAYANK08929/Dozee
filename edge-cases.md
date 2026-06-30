# Edge cases — trying to break the dashboard

Failure modes, how the code handles each today, and the correct behaviour.

### 1. Zero or negative amount — *Guarded*
- **Now:** The add-expense form rejects amounts ≤ 0 with an inline error, so bad values never enter state. Seed data has none.
- **Should:** Block at the input layer (done) **and** validate again before any aggregation, so an amount arriving from a future import/API path can never poison category totals.

### 2. A rate is null, undefined, or zero — *Handled*
- **Now:** `toUSD()` returns `null` for any missing, non-numeric, or ≤0 rate. Such rows render "—", are excluded from totals, and a "skipped" counter appears in the summary.
- **Should:** Exactly this — never silently coerce a bad rate to 1 or 0 (both corrupt the board report). Surfacing the skipped count keeps the finance head honest about coverage.

### 3. Merchant name with special characters / emoji — *Handled*
- **Now:** React escapes all rendered text, so `<script>`, quotes, and emoji display literally with no injection risk. Length is capped at 60 chars.
- **Should:** Current behaviour is correct. Long names truncate via table layout rather than breaking it.

### 4. Empty form fields on submit — *Handled*
- **Now:** Each required field is validated; the first failure shows a specific message ("Enter a merchant name", etc.) and submission is blocked.
- **Should:** Field-specific messaging is the right call over a single generic "invalid form" error.

### 5. Very large amount causing display overflow — *Partial*
- **Now:** Numbers use tabular figures and locale grouping. A 12-digit amount still fits typical widths but could crowd the mono column on very narrow screens.
- **Should:** Add compact notation (e.g. `$1.2M`) above a threshold, with the full value on hover, and a sane max on the amount input.

### 6. Filter returns no results — *Handled*
- **Now:** An explicit empty state tells the user no rows match and how to clear the filter — never a blank table.
- **Should:** Correct. The empty state is an instruction, not just "no data".

### 7. Narrow mobile screen — *Partial*
- **Now:** Layout is responsive: the form reflows to two columns and low-priority columns hide below 720px.
- **Should:** Acceptable for a Friday demo. A card-per-expense layout would beat a cramped table on the smallest phones.

### 8. Adding a currency with no rate (the "25th currency") — *By design*
- **Now:** The form only offers the 10 supported currencies, so a user can't enter an unsupported one. If one arrived via data, `toUSD()` would skip it safely.
- **Should:** Adding a currency is a one-line change to `RATES`; the dropdown and all math pick it up automatically.

### 9. Floating-point rounding drift across many conversions — *Mitigated*
- **Now:** `round2()` uses an epsilon nudge and rounding only happens at display/total boundaries, not on every intermediate add — so category totals and the overall total reconcile.
- **Should:** For real money, move to integer minor-units (cents) end-to-end to eliminate float entirely. Acceptable at this dataset size.

---

**The one I'd flag:** a *null rate silently defaulting to 1.0* is the dangerous bug here — it wouldn't crash, it would just quietly understate a foreign expense in a board report. That's why bad rates are skipped and counted, never coerced.
