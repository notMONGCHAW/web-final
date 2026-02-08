# Web Programming Final — Two‑Page Code Template (JS + PHP + SQL)

---

## PAGE 1 — PHP + SQL

### 1) PHP — min units + leftover + wasted money

```php
<?php
// Core idea: total → ceil → leftover → wasted
function calc($people, $perPerson, $capacity, $unitPrice=null, $seatPrice=null){
  // 1) total required
  $total = $people * $perPerson;
  // 2) minimum units (always ceil)
  $units = (int) ceil($total / $capacity);
  // 3) leftover/empty
  $left  = $units * $capacity - $total;

  // 4) wasted money (pick ONE based on question)
  $wasted = 0;
  // A) fixed unit price (pizza/venue/screen)
  if ($unitPrice !== null) $wasted = $left * ($unitPrice / $capacity);
  // B) per person/seat/ticket price
  if ($seatPrice !== null) $wasted = $left * $seatPrice;

  return [$units, $left, $wasted];
}

// Example call (pizza):
// list($pizzas,$left,$waste)=calc($students,$slicesPerStudent,$slicesPerPizza,1050,null);
?>
```

---

### 2) PHP‑MySQL — connection + select + update + group

```php
<?php
$conn = new mysqli("localhost","username","password","dbname");
if ($conn->connect_error) die("Connection failed");

// GROUP BY count (A/B/C/D etc.)
$sql = "SELECT LetterGrade, COUNT(*) total FROM student_final GROUP BY LetterGrade";
$res = $conn->query($sql);
while($row = $res->fetch_assoc()){
  echo $row["LetterGrade"]." ".$row["total"]."<br>";
}

// UPDATE with condition (below threshold)
$conn->query("UPDATE student_final SET LetterGrade='C'
              WHERE Grade < 75 AND LetterGrade <> 'D'");

// Conditional bonus (cap limit)
$conn->query("UPDATE student_final SET Grade = Grade + 5
              WHERE Grade > 80 AND Grade + 5 <= 90");

// GROUP BY + ORDER (popularity)
$conn->query("SELECT CourseTitle, COUNT(*) total
              FROM student_final GROUP BY CourseTitle ORDER BY total DESC");
?>
```

```php
<?php
// Create DB + table + insert (short pattern)
$conn = new mysqli("localhost","username","password");
$conn->query("CREATE DATABASE IF NOT EXISTS uiuweb_final");
$conn->query("USE uiuweb_final");

$conn->query("CREATE TABLE IF NOT EXISTS student_final(
  StudentID INT PRIMARY KEY,
  StudentName VARCHAR(50),
  CourseID INT,
  CourseTitle VARCHAR(80),
  Grade INT,
  LetterGrade CHAR(1)
)");

// Insert rows (repeat VALUES if needed)
$conn->query("INSERT INTO student_final VALUES
 (1,'Karim Uddin',101,'Web Programming',85,'B'),
 (2,'Rahim Ahmed',101,'Web Programming',92,'A')");
?>
```

---

### 3) SQL Snippets (quick recall)

```sql
-- Count per grade/rating
SELECT LetterGrade, COUNT(*) AS total
FROM student_final
GROUP BY LetterGrade;

-- Update based on conditions
UPDATE student_final
SET LetterGrade='C'
WHERE Grade < 75 AND LetterGrade <> 'D';

-- Bonus with cap
UPDATE student_final
SET Grade = Grade + 5
WHERE Grade > 80 AND Grade + 5 <= 90;

-- Group + order
SELECT CourseTitle, COUNT(*) AS total
FROM student_final
GROUP BY CourseTitle
ORDER BY total DESC;
```

```sql
-- Create DB/table + insert (template)
CREATE DATABASE IF NOT EXISTS uiuweb_final;
USE uiuweb_final;

CREATE TABLE student_final(
  StudentID INT PRIMARY KEY,
  StudentName VARCHAR(50),
  CourseID INT,
  CourseTitle VARCHAR(80),
  Grade INT,
  LetterGrade CHAR(1)
);

INSERT INTO student_final VALUES
(1,'Karim Uddin',101,'Web Programming',85,'B');
```

---

### 4) SQL — revenue + category average label (if asked)

```sql
-- Total revenue per category
SELECT CategoryName, SUM(Revenue) AS total_revenue
FROM sales_data
GROUP BY CategoryName;

-- Update category if revenue is low
UPDATE sales_data
SET CategoryName = 'Low Performing'
WHERE Revenue < 40000;

-- Add bonus revenue if high (10%)
UPDATE sales_data
SET Revenue = Revenue * 1.10
WHERE Revenue > 70000;

-- Top Seller vs Regular Seller (compare to category average)
SELECT s.ProductName, s.CategoryName,
CASE
  WHEN s.Revenue > c.avg_rev THEN 'Top Seller'
  ELSE 'Regular Seller'
END AS SellerLabel
FROM sales_data s
JOIN (
  SELECT CategoryID, AVG(Revenue) AS avg_rev
  FROM sales_data
  GROUP BY CategoryID
) c ON s.CategoryID = c.CategoryID;
```

---

## PAGE 2 — JavaScript

### 1) Minimal HTML

```html
<!-- index.html (minimal) -->
<input id="in" type="text" />
<button onclick="go()">Submit</button>
<div id="out"></div>
```

### 2) Helper functions (reuse in any JS question)

```js
const $ = (id) => document.getElementById(id);
const num = (id) => Number($(id).value);
const show = (msg) => { $("out").innerText = msg; };
const clamp = (v, lo, hi) => Math.min(hi, Math.max(lo, v));

// random in range [min, max]
const rand = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;

// reset helpers
const resetInput = () => { $("in").value = ""; };
const disableInput = () => { $("in").disabled = true; };
```

### 3) Pattern A — Secret number / compare

```js
let attempts = 0;
const secret = rand(500, 5000);

function go(){
  attempts++;
  const guess = num("in");
  if (guess > secret) show("Too high!");
  else if (guess < secret) show("Too low!");
  else { show("Correct!"); disableInput(); }

  if (attempts >= 5 && guess !== secret) { show("Out of guesses!"); disableInput(); }
}
```

### 4) Pattern B — Tracker / running total

```js
let total = 0;
let attempts = 0;
const goal = 2000;

function go(){
  attempts++;
  total += num("in");

  if (total <= 800) show("You’re off to a healthy start!");
  else if (total <= 1600) show("Good progress, keep it balanced!");
  else if (total < 2000) show("Almost at your limit!");
  else show("Goal reached! Stay mindful!");

  if (attempts > 10 && total < goal) show("Be cautious of frequent snacking!");
}
```

### 5) Pattern C — Password strength

```js
function scorePassword(p){
  let s = 0;
  if (p.length >= 6) s += Math.floor(p.length / 2) * 10; // +10 per 2 chars
  if (/[A-Z]/.test(p)) s += 15;
  if (/[a-z]/.test(p)) s += 15;
  if (/[0-9]/.test(p)) s += 20;
  if (/[!@#$%^&*]/.test(p)) s += 25;
  return s;
}

function strengthLabel(score){
  if (score <= 30) return "Very Weak";
  if (score <= 50) return "Weak";
  if (score <= 70) return "Medium";
  if (score <= 90) return "Strong";
  return "Very Strong";
}

function go(){
  const p = $("in").value;
  const sc = scorePassword(p);
  if (sc >= 100) show("Perfect Password!");
  else show(strengthLabel(sc));
}
```
