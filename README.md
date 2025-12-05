# VettyAssignment

Assumptions:
1.We have two tables: transactions and items.

2.Times are stored as timestamptz (UTC).

3.“Valid” match = a transaction references an item by (store_id, item_id); where needed we join items to read item_name.

4.Negative intervals (refund before purchase) are ignored.

5.When multiple transactions tie for “earliest”, two behaviors are offered (preserve ties or pick one deterministically). I note which query does which.

Overall approach:
1.Use window functions (ROW_NUMBER() / RANK()) to find ordered purchases per buyer or per store where we need positional logic (first, second).

2.Use aggregation (MIN, COUNT, MIN(EXTRACT(EPOCH...))) to compute monthly counts, first purchase timestamps, and shortest intervals.

3.Use LEFT JOIN items only when we need item_name (so we don’t drop unmatched transactions).

4.Use safe date filters with explicit ranges (e.g., >= '2020-10-01'::timestamptz AND < '2020-11-01'::timestamptz) for month boundaries.

