# What we built — a note for the CEO

*Plain English, from the analyst. One page.*

## What it is and why it matters

Our head of finance currently rebuilds the multi-currency expense summary by hand every month, Googling rates as she goes. That is slow and easy to get wrong. This tool does it automatically: drop in the month's expenses in any of our ten currencies and it produces a clean, board-ready USD summary — category totals, the overall total, and where the money actually goes — in seconds. It turns a recurring manual chore into something reliable and repeatable.

## The three trade-offs I made

1. **Shipped working over polished.** I chose a no-build static app so it deploys instantly and a teammate can read it in one file. The cost is a small load-time flash; the benefit is it runs today at a real URL.
2. **In-memory over a database.** Added expenses don't persist yet. This let me get every required feature working in the time window. Persistence is the obvious next step, not a rewrite.
3. **Safety over silent guesses.** If a currency rate is missing, the app skips that expense and says so, rather than inventing a number. A visible gap is safer than a wrong board figure.

## What I'd prioritise next sprint

1. **Persistence (save expenses).** So finance's work survives a refresh. *Impact:* the tool becomes usable for real monthly close, not just a demo.
2. **Live rate feed with the snapshot as fallback.** *Impact:* removes the last manual step — no more Googling rates — while staying auditable.
3. **Export to the board deck (CSV/PDF).** *Impact:* the summary goes straight into the monthly report, saving finance another copy-paste pass.

**Honest status:** the dashboard and all required features work. Persistence and live rates are deliberately deferred and scoped above.
