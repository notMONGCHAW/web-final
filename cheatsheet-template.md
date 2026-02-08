## Barebones Setup (in case you forget)

### 0) Minimal folder structure

```
project/
  index.html
  script.js
  process.php
```

### 1) Minimal HTML (works for JS + PHP)

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <title>UIU Web Final</title>
</head>
<body>
  <form method="post" action="process.php">
    <input id="in" name="in" type="text" />
    <button type="submit">Submit</button>
  </form>
  <div id="out"></div>

  <script src="script.js"></script>
</body>
</html>
```

### 2) Minimal JS file

```js
const $ = (id) => document.getElementById(id);
function show(msg){ $("out").innerText = msg; }
function go(){
  const v = Number($("in").value);
  show(`You entered: ${v}`);
}
```

### 3) Minimal PHP file

```php
<?php
if ($_SERVER["REQUEST_METHOD"] === "POST") {
  $v = $_POST["in"] ?? "";
  echo "You entered: " . htmlspecialchars($v);
}
?>
```

### 4) Minimal DB connection (MySQLi)

```php
<?php
$conn = new mysqli("localhost","username","password","dbname");
if ($conn->connect_error) die("Connection failed");
?>
```

---

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

### 5) SQL — Sales analytics (January 2024)

```sql
-- Total quantity + revenue per product
SELECT product_id,
       SUM(quantity_sold) AS total_qty,
       SUM(total_price)   AS total_revenue
FROM sales
GROUP BY product_id;

-- Top 2 days by revenue
SELECT sale_date, SUM(total_price) AS total_revenue
FROM sales
GROUP BY sale_date
ORDER BY total_revenue DESC
LIMIT 2;

-- Avg quantity per product, only if sold on >= 2 different days
SELECT product_id, AVG(quantity_sold) AS avg_qty
FROM sales
GROUP BY product_id
HAVING COUNT(DISTINCT sale_date) >= 2;
```

---

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

### 6) Pattern D — Electricity bill (rates + surcharge + validation)

```js
function go(){
  const location = $("location").value; // Dhaka, Chattogram, Other
  const urban = $("urban").checked;
  const rural = $("rural").checked;
  const units = Number($("units").value);

  if (!location) return show("Please select a location.");
  if (urban === rural) return show("Select exactly one area type.");
  if (!Number.isFinite(units) || units <= 0) return show("Units must be a positive number.");

  const rateTable = {
    Dhaka:      { urban: 7.5, rural: 6.5 },
    Chattogram: { urban: 7.0, rural: 6.0 },
    Other:      { urban: 6.5, rural: 5.5 }
  };

  const rate = (urban ? rateTable[location].urban : rateTable[location].rural);
  let bill = units * rate;

  let surcharge = 0;
  if (bill >= 500 && bill <= 1000) surcharge = bill * 0.05;
  else if (bill > 1000) surcharge = bill * 0.10;

  const total = (bill + surcharge).toFixed(2);
  alert(`Final bill: ${total} taka`);
}
```

---

### 1) PHP — Average speed (uphill + downhill)

```php
<?php
// timeUp in minutes, speedUp/speedDown in km/h
function calculate($timeUpMin, $speedUp, $speedDown){
  $timeUpHr = $timeUpMin / 60;
  $distanceUp = $speedUp * $timeUpHr;
  $timeDownHr = $distanceUp / $speedDown; // same distance back

  $totalDistance = 2 * $distanceUp;
  $totalTime = $timeUpHr + $timeDownHr;

  return (int) round($totalDistance / $totalTime);
}
?>
```

### 2) PHP — Password strength (Strong/Medium/Poor/Very Poor)

```php
<?php
function strength($p){
  $len = strlen($p);
  $hasUpper = preg_match('/[A-Z]/', $p);
  $hasLower = preg_match('/[a-z]/', $p);
  $hasDigit = preg_match('/[0-9]/', $p);
  $hasSpecial = preg_match('/[^A-Za-z0-9]/', $p);
  $endsStar = str_ends_with($p, '*');

  if ($len >= 10 && $hasUpper && $hasLower && $hasDigit && $hasSpecial && $endsStar) return "Strong";
  if ($len >= 8 && $hasUpper && $hasLower && $hasDigit) return "Medium";
  if ($len >= 6 && $hasUpper) return "Poor";
  return "Very Poor";
}
?>
```
