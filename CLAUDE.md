# Standing rules for this fork

This fork runs a simulated day-trading book. A new session starts here; the
user may open with just "scan".

## Safety: simulation only

- The Interactive Brokers connector is attached to a LIVE, real-money account.
  Use it for market data only: `search_contracts`, `get_price_snapshot`,
  `get_price_history`.
- Never call `create_order_instruction`, `delete_order_instruction`, or any
  alert, watchlist or screener write tool unless the user asks for that one
  action in this session. Where a SKILL.md says to send an order to a broker,
  do the simulated step below instead.
- Limits live in `trader/config.json`. Do not change them unless asked.

## How to run a step

The eleven skills are the `trader-*` folders at the root, not registered
skills. Before a step, read that folder's `SKILL.md`, then run its Python tool
from the repository root so state lands in `trader/`.

Order of one trade: scanner, news, entry, exit, timemachine, budget, risk,
broker. "scan" means: `scan.py universe` if `trader/universe.txt` is missing,
then `scan.py daily` before the open or `scan.py orb` after 09:45 New York.

## Simulated fills

1. Get a fresh quote (IBKR snapshot or `alerts.py quote SYM --write`) so
   `trader/prices.json` is under 15 minutes old.
2. `check.py`, then `ticket.py show`. Show one ticket, ask yes or no.
3. `ticket.py approve --index N --reply "<the reply, word for word>"`.
4. Entry fill: `ticket.py result --index N --status placed --broker-id sim
   --filled-qty Q --filled-price P`, with P the quote from step 1.
5. Exit fill (stop, target, or flat by 15:55 New York):
   `ticket.py fill --symbol S --side sell --qty Q --price P`.

## Housekeeping

- Print the top 10 rows of any scan; leave the full output in files.
- End of day: report profit and loss, commit `trader/`, open a pull request.
  The repository is public: no names, emails or account numbers in `trader/`.
- Tests: `cd tests && python3 -m unittest discover -s .`
