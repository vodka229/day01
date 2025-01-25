## day 01

#### Exercise 00

task text:
Please write a SQL statement which returns menu’s identifier and pizza names from `menu` table and person’s identifier and person name from `person` table in one global list (with column names as presented on a sample below) ordered by object_id and then by object_name columns.

code:
```
SELECT 
    id AS object_id,
    pizza_name AS object_name
FROM menu
UNION ALL
SELECT
    id AS object_id,
    name AS object_name
FROM person
ORDER BY object_id, object_name;
```
output:
![](/img/1.png)

#### Exercise 01

task text:
Please modify a SQL statement from “exercise 00” by removing the object_id column. Then change ordering by object_name for part of data from the `person` table and then from `menu` table (like presented on a sample below). Please save duplicates!

code:
```
SELECT object_name
FROM (
    SELECT 
        name AS object_name, 1 as sort_order
    FROM person
    UNION ALL
    SELECT
        pizza_name AS object_name, 2 as sort_order
    FROM menu
) as nya
ORDER BY sort_order, object_name;
```

output:
![](/img/2.png)

#### Exercise 02

task text:
Please write a SQL statement which returns unique pizza names from the `menu` table and orders them by pizza_name column in descending mode. Please pay attention to the Denied section.

code:
```
SELECT pizza_name
FROM menu
EXCEPT
SELECT pizza_name
FROM menu
WHERE pizza_name LIKE '%mushroom%'
ORDER BY pizza_name DESC;
```

output:
![](/img/3.png)

#### Exercise 03

task text:
Please write a SQL statement which returns common rows for attributes order_date, person_id from `person_order` table from one side and visit_date, person_id from `person_visits` table from the other side (please see a sample below). In other words, let’s find identifiers of persons, who visited and ordered some pizza on the same day. Actually, please add ordering by action_date in ascending mode and then by person_id in descending mode.

code:
```
SELECT order_date AS action_date, person_id
FROM person_order
INTERSECT
SELECT visit_date AS action_date, person_id
FROM person_visits
ORDER BY action_date ASC, person_id DESC;
```

output:
![](/img/4.png)

#### Exercise 04

task text:
Please write a SQL statement which returns a difference (minus) of person_id column values with saving duplicates between `person_order` table and `person_visits` table for order_date and visit_date are for 7th of January of 2022

code:
```
SELECT person_id
FROM (
    SELECT person_id, 1 AS nya
    FROM person_order
    WHERE order_date = '2022-01-07'
    EXCEPT ALL
    SELECT person_id, -1 AS nya
    FROM person_visits
    WHERE visit_date = '2022-01-07'
) AS Combo
GROUP BY person_id
HAVING SUM(nya) > 0
ORDER BY person_id DESC;
```

output:
![](/img/5.png)

#### Exercise 05

task text:
Please write a SQL statement which returns all possible combinations between `person` and `pizzeria` tables and please set ordering by person identifier and then by pizzeria identifier columns. Please take a look at the result sample below. Please be aware column's names can be different for you.

code:
```
SELECT
    p.id AS person_id,
    p.name AS person_name,
    p.age AS age,
    p.gender AS gender,
    p.address AS address,
    pz.id AS pizzeria_id,
    pz.name AS pizzeria_name,
    pz.rating AS rating
FROM
    person p
CROSS JOIN
    pizzeria pz
ORDER BY
    p.id ASC,
    pz.id ASC;
```

output:
![](/img/6.png)

#### Exercise 06

task text:
Let's return our mind back to exercise #03 and change our SQL statement to return person names instead of person identifiers and change ordering by action_date in ascending mode and then by person_name in descending mode. Please take a look at a data sample below.

code:
```
SELECT p.name AS person_name, po.order_date AS action_date
FROM person p
INNER JOIN person_order po ON p.id = po.person_id
WHERE (po.order_date,po.person_id) IN (
    SELECT order_date, person_id
    FROM person_order
    INTERSECT
    SELECT visit_date, person_id
    FROM person_visits
)
ORDER BY action_date ASC, person_name DESC;
```

output:
![](/img/7.png)

#### Exercise 07

task text:
Please write a SQL statement which returns the date of order from the `person_order` table and corresponding person name (name and age are formatted as in the data sample below) which made an order from the `person` table. Add a sort by both columns in ascending mode.

code:
```
SELECT
    po.order_date,
    p.name || ' (age:' || p.age || ')' AS person_information
FROM
    person_order po
INNER JOIN
    person p ON po.person_id = p.id
ORDER BY
    po.order_date ASC,
    person_information ASC;
```

output:
![](/img/8.png)

#### Exercise 08

task text:
Please rewrite a SQL statement from exercise #07 by using NATURAL JOIN construction. The result must be the same like for exercise #07.

code:
```
SELECT
    po.order_date,
    p.name || ' (age:' || p.age || ')' AS person_information
FROM
    (SELECT order_date, person_id AS id FROM person_order) po
NATURAL JOIN
    person p
ORDER BY
    po.order_date ASC,
    person_information ASC;
```

output:
![](/img/9.png)

#### Exercise 09

task text:
Please write 2 SQL statements which return a list of pizzerias names which have not been visited by persons by using IN for 1st one and EXISTS for the 2nd one.

code:
```
SELECT name
FROM pizzeria
WHERE id NOT IN (SELECT DISTINCT pizzeria_id FROM person_visits);
```

```
SELECT name
FROM pizzeria p
WHERE NOT EXISTS (
    SELECT 1
    FROM person_visits pv
    WHERE pv.pizzeria_id = p.id
);
```

output:
![](/img/10.png)

#### Exercise 10

task text:
Please write a SQL statement which returns a list of the person names which made an order for pizza in the corresponding pizzeria. 
The sample result (with named columns) is provided below and yes ... please make ordering by 3 columns (`person_name`, `pizza_name`, `pizzeria_name`) in ascending mode.

code:
```
SELECT
    p.name AS person_name,
    m.pizza_name AS pizza_name,
    pz.name AS pizzeria_name
FROM
    person p
INNER JOIN
    person_order po ON p.id = po.person_id
INNER JOIN
    menu m ON po.menu_id = m.id
INNER JOIN
    pizzeria pz ON m.pizzeria_id = pz.id
ORDER BY
    person_name ASC,
    pizza_name ASC,
    pizzeria_name ASC;
```

output:
![](/img/11.png)