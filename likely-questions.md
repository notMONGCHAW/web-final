# Likely Questions (Based on Last 3 Exams)

> The final typically has **2 PHP + 1 SQL/PHP‑MySQL** question. These patterns repeated across all three papers.

## Highest Probability
1) **PHP calculation** using `ceil()` + leftover + wasted money
   - Pizzas / Venues / Screens
   - Inputs from HTML text fields
   - Output: total units, leftover/empty, wasted money

2) **PHP‑MySQL** full flow (create DB/table + insert data + queries)
   - `GROUP BY` counts or sums
   - `UPDATE` rows based on conditions
   - Bonus or cap logic
   - `ORDER BY` on grouped results

## Medium Probability
3) **JavaScript input + feedback + attempts** (if JS appears)
   - Ranged feedback messages
   - Attempts counter & “too many attempts” message

## Common Variations
- **Wasted money** formula changes:
  - Fixed price per unit → use per‑slot cost = unitPrice / capacity
  - Price per person/seat/ticket → wasted = empty × seatPrice

- **Update logic** might change thresholds:
  - “if below X and rating not D → set to C”
  - “if above Y, add bonus but cap at Z”

- **Group queries** might switch between:
  - COUNT per grade/rating
  - SUM revenue per category
  - COUNT per course/department

## Most Likely Topics to Appear Again
- `ceil()` calculation problems in PHP
- SQL `GROUP BY` + `COUNT()` or `SUM()`
- SQL `UPDATE` with conditional rules
- PHP‑MySQL output loops (`fetch_assoc`)
