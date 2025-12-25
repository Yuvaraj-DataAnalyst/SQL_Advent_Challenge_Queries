
# 🎄  SQL Advent Challenge


## 📌 Challenge Overview

This repository contains my solutions to the **SQL Advent Challenge**, a festive, scenario-based SQL challenge designed to strengthen SQL fundamentals and advanced analytical skills.
The problems simulate real-world business scenarios using holiday-themed datasets and require clean, efficient SQL solutions.

---

## 🧩 Challenge Details

* Total Questions Solved: **24**
* Difficulty Levels: **Easy, Medium, Hard**
* Focus:

  * Writing correct and optimized SQL queries
  * Applying analytical and window functions
  * Solving business-style data problems
  * Improving SQL readability and logic

---

## 🎯 Difficulty Levels (Easy / Medium / Hard)

---

### 🟢 Easy

#### 1. Top 7 Reindeers by Rank

**Question:**
Every year, the city of Whoville conducts a Reindeer Run to find the best reindeers for Santa's Sleigh. Return the name and rank of the top 7 reindeers.

```sql
SELECT name, rank
FROM reindeer_run_results
ORDER BY rank ASC
LIMIT 7;
```

---

#### 2. Delivered Toys

**Question:**
Return the toy ID and toy name of toys that have already been delivered.

```sql
SELECT tp.toy_id, tp.toy_name
FROM toy_production tp
JOIN toy_delivery td
ON tp.toy_id = td.toy_id;
```

---

#### 4. Most Energy-Efficient Decorations

**Question:**
Find the top 5 decorations with the lowest energy cost per hour.

```sql
SELECT decoration_name, energy_cost_per_hour
FROM hall_decorations
ORDER BY energy_cost_per_hour ASC
LIMIT 5;
```

---

#### 7. Unique Snowflake Types

**Question:**
How many unique snowflake types were recorded on December 24, 2025?

```sql
SELECT COUNT(DISTINCT flake_type) AS unique_flake_types
FROM snowfall_log
WHERE CAST(fall_time AS DATE) = '2025-12-24';
```

---

#### 10. Average Baking Time per Oven

**Question:**
Find the average baking time per oven rounded to one decimal place.

```sql
SELECT oven_id,
       ROUND(AVG(baking_time_minutes), 1) AS avg_time
FROM cookie_batches
GROUP BY oven_id
ORDER BY oven_id;
```

---

#### 13. Naughty or Nice Score Extremes

**Question:**
Return the lowest and highest behavior scores recorded.

```sql
SELECT
    MIN(behavior_score) AS lowest_score,
    MAX(behavior_score) AS highest_score
FROM behavior_scores;
```

---

#### 16. Cozy Task Selection

**Question:**
Find all tasks marked as “Work From Home” or “Low Priority”.

```sql
SELECT *
FROM daily_tasks
WHERE task_type = 'Work From Home'
   OR priority = 'Low';
```

---

#### 19. Wrapping Paper Usage

**Question:**
Return the total wrapping paper used for gift-wrapped and delivered orders.

```sql
SELECT SUM(paper_used_meters) AS total_paper_used
FROM holiday_orders
WHERE gift_wrap = 1
  AND delivery_status = 'Delivered';
```

---

#### 22. Penguins Not Choosing Evening Ride

**Question:**
Return all penguins who did NOT choose the Evening Ride.

```sql
SELECT signup_id, penguin_name, ride_time
FROM sleigh_ride_signups
WHERE ride_time <> 'Evening';
```

---

### 🟡 Medium

#### 5. Elf Vacation Status

**Question:**
List all elves and their return date. If not returned, show “Still resting”.

```sql
SELECT e.elf_name,
       CASE
           WHEN v.return_date IS NULL THEN 'Still resting'
           ELSE v.return_date
       END AS return_date
FROM elves e
LEFT JOIN vacations v
ON e.elf_id = v.elf_id
ORDER BY e.elf_id;
```

---

#### 8. Combined Decorations List

**Question:**
Combine Christmas trees and light sets into a single list.

```sql
SELECT *
FROM storage_trees
UNION ALL
SELECT *
FROM storage_lights;
```

---

#### 9. Tinsel–Light Pairings

**Question:**
Generate every possible tinsel and light color combination.

```sql
SELECT
    l.*,
    t.*,
    CONCAT(l.color_name, '-', t.color_name) AS full_combo
FROM light_colors l
CROSS JOIN tinsel_colors t;
```

---

#### 11. Sweater Inventory Cleanup

**Question:**
Return sweater names and properly formatted colors despite inconsistent casing.

```sql
SELECT
    item_name,
    UPPER(SUBSTR(color, 1, 1)) || LOWER(SUBSTR(color, 2)) AS clean_color
FROM winter_clothing
WHERE LOWER(item_name) LIKE '%sweater%';
```

---

#### 14. Focus Challenge End Date

**Question:**
Calculate the focus end date, 14 days after the start date.

```sql
SELECT *,
       DATE(start_date, '+14 days') AS focus_end_date
FROM focus_challenges;
```

---

#### 17. Task Noise Classification

**Question:**
Classify tasks as Calm (<50 noise level) or Chaotic.

```sql
SELECT *,
       CASE
           WHEN noise_level < 50 THEN 'Calm'
           ELSE 'Chaotic'
       END AS status
FROM evening_tasks;
```

---

#### 20. Cocoa Break Logs

**Question:**
Return cocoa breaks with cocoa type and location details.

```sql
SELECT
    cl.log_id,
    cl.break_id,
    ct.cocoa_name,
    l.location_name
FROM cocoa_logs cl
JOIN cocoa_types ct ON cl.cocoa_id = ct.cocoa_id
JOIN break_schedule bs ON cl.break_id = bs.break_id
JOIN locations l ON bs.location_id = l.location_id;
```

---

### 🔴 Hard

#### 3. Most Evil Prank per Target

**Question:**
Return the most evil prank per target. Break ties using the most recent prank.

```sql
WITH prankrank AS (
    SELECT
        target_name,
        evilness_score,
        created_at,
        RANK() OVER (
            PARTITION BY target_name
            ORDER BY evilness_score DESC, created_at DESC
        ) AS targets
    FROM grinch_prank_ideas
)
SELECT target_name, evilness_score
FROM prankrank
WHERE targets = 1;
```

---

#### 6. Snowfall Quartiles

**Question:**
Bucket ski resorts into quartiles based on annual snowfall.

```sql
WITH AnnualSnowfall AS (
    SELECT
        resort_id,
        resort_name,
        SUM(snowfall_inches) AS total_snowfall
    FROM resort_monthly_snowfall
    GROUP BY resort_id, resort_name
)
SELECT
    resort_id,
    resort_name,
    total_snowfall,
    NTILE(4) OVER (ORDER BY total_snowfall DESC) AS quartile
FROM AnnualSnowfall
ORDER BY quartile, total_snowfall DESC;
```

---

#### 12. Most Active Chat Users

**Question:**
Find the most active user(s) each day. Return ties if present.

```sql
WITH daily_counts AS (
    SELECT
        CAST(nm.sent_at AS DATE) AS msg_date,
        nu.user_id,
        nu.user_name,
        COUNT(nm.message_id) AS msg_count
    FROM npn_messages nm
    JOIN npn_users nu ON nm.sender_id = nu.user_id
    GROUP BY CAST(nm.sent_at AS DATE), nu.user_id, nu.user_name
),
ranked_users AS (
    SELECT *,
           RANK() OVER (PARTITION BY msg_date ORDER BY msg_count DESC) AS rnk
    FROM daily_counts
)
SELECT msg_date, user_id, user_name, msg_count
FROM ranked_users
WHERE rnk = 1
ORDER BY msg_date;
```

---

#### 15. Mischief Score Changes

**Question:**
Calculate day-over-day change in mischief score.

```sql
SELECT
    log_date,
    mischief_score,
    LAG(mischief_score) OVER (ORDER BY log_date) AS previous_score,
    mischief_score -
    LAG(mischief_score) OVER (ORDER BY log_date) AS diff
FROM grinch_mischief_log
ORDER BY log_date;
```

---

#### 18. Quiz Score Improvement

**Question:**
Find each subject’s first score, last score, and improvement.

```sql
SELECT DISTINCT
    subject,
    FIRST_VALUE(score) OVER (PARTITION BY subject ORDER BY quiz_date) AS first_score,
    LAST_VALUE(score) OVER (
        PARTITION BY subject
        ORDER BY quiz_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_score,
    LAST_VALUE(score) OVER (
        PARTITION BY subject
        ORDER BY quiz_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) -
    FIRST_VALUE(score) OVER (PARTITION BY subject ORDER BY quiz_date) AS improvement
FROM daily_quiz_scores;
```

---

#### 21. Running Total of Stories

**Question:**
Calculate the running total of stories told over time.

```sql
SELECT
    log_date,
    SUM(stories_shared) OVER (ORDER BY log_date) AS running_sum
FROM story_log
ORDER BY log_date;
```

---

#### 23. Top Gingerbread Builders

**Question:**
Return the top builders who used the most distinct candy types.

```sql
SELECT builder_name
FROM gingerbread_designs
GROUP BY builder_name
HAVING COUNT(DISTINCT candy_type) >= 4;
```

---

## 🛠️ SQL Concepts Used

`SELECT`, `WHERE`, `ORDER BY`, `LIMIT`, `JOIN`, `LEFT JOIN`,
`GROUP BY`, `HAVING`, `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`,
`CASE`, `DISTINCT`, `UNION ALL`, `CROSS JOIN`,
CTEs, Window Functions (`RANK`, `NTILE`, `LAG`, `FIRST_VALUE`, `LAST_VALUE`),
Date & string functions, Running totals, Ranking logic
---

## 📈 Key Learnings
* Applied SQL across increasing difficulty levels
* Mastered analytical window functions
* Improved query structuring and readability
* Learned real-world SQL problem-solving patterns
* Built strong portfolio-ready SQL projects
---

