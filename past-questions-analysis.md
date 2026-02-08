# Analysis of Past Questions & Recurring Topics

## Exam 1 (2024 Fall)
- **JS**: Secret number guessing (random, compare, attempts)
- **PHP**: Pizza slices (ceil, leftover, wasted money via per‑slice cost)
- **PHP‑MySQL**: Grades table
  - Count by letter grade
  - Update grade if below threshold
  - Bonus points with cap
  - Course popularity (COUNT + ORDER)

## Exam 2 (2025 ?)
- **JS**: Password strength scoring (rules + attempts)
- **PHP**: Venue booking (ceil, empty seats, wasted money by cost per person)
- **PHP‑MySQL**: Employee table
  - Count by performance rating
  - Update rating if salary below threshold
  - Salary bonus with cap
  - Department popularity (COUNT + ORDER)

## Exam 3 (2025 Summer)
- **JS**: Calorie tracker (accumulate + ranges + attempts)
- **PHP**: Movie screens (ceil, empty seats, wasted by ticket price)
- **PHP‑MySQL**: Sales data
  - SUM revenue per category
  - Update category if revenue low
  - Bonus revenue if high
  - Top seller vs regular (comparison to category average)

---

## Recurring Topics (Most Frequent)
1) **PHP calculations**
   - Always includes `ceil()` + leftover
   - Wasted money depends on per‑seat vs per‑unit cost

2) **SQL/DB tasks**
   - `GROUP BY` with `COUNT()` or `SUM()`
   - `UPDATE` with conditions
   - Sorting grouped results with `ORDER BY`
   - Sometimes uses subquery/average for labels

3) **JS UI logic** (if asked)
   - Input + feedback + ranges
   - Attempts limit message

---

## Highest Probability Now (Given 2 PHP + 1 SQL)
- **PHP #1**: ceil + leftover + wasted money (pizza/venue/screen style)
- **PHP #2**: another scenario with the same formula but different labels
- **SQL/PHP‑MySQL**:
  - Create DB/table, insert sample data
  - One `GROUP BY` count/sum
  - One conditional `UPDATE`
  - One bonus/cap update
  - One grouped popularity query

---

## Quick Memorization Hooks
- “minimum units” → `ceil()`
- “leftover/empty” → `units*capacity - required`
- “wasted money” → leftover × (price per slot or per seat)
- “grouped totals” → `GROUP BY` + `COUNT/SUM`
- “update with rules” → `UPDATE ... WHERE ...`
