# SQL - Agoge

SQL tutorial by @tonyfg
Date: 12 July 2017

## Contents

- 1 LIMIT / OFFSET
   - 1.1 Getting the first 10 results of a query
   - 1.2 Skipping the first 5 results, and getting the next
- 2 ORDER BY
   - 2.1 By default ORDER BY uses ASCending order:
   - 2.2 Sorting in DESCending order:
   - 2.3 Sorting by the result of a function call on a column
   - 2.4 Ordering by multiple columns
- 3 JOIN
   - 3.1 Getting data from 2 tables using JOIN
- 4 LEFT JOIN
   - 4.1 Trying with a regular JOIN
   - 4.2 Trying with LEFT JOIN
- 5 RIGHT JOIN
   - 5.1 Trying with a regular JOIN
   - 5.2 Trying with a RIGHT JOIN
- 6 FULL JOIN
   - 6.1 Trying with a regular JOIN
   - 6.2 Trying with a FULL JOIN
- 7 GROUP
   - 7.1 Using GROUP with aggregation functions
- 8 Sub-SELECTS
   - site 8.1 Example: Getting all patients that don’t belong to a certain
- 9 Indexes and text searching
   - 9.1 EXPLAIN ANALYZE
   - 9.2 Creating an index
   - 9.3 Making sure your query uses the index
   - 9.4 B-Tree index limitations
   - 9.5 What if I need to do other types of searches?
      - 9.5.1 Full-text indexes
      - 9.5.2 Trigram indexes
- 10 UNION 8
   - 10.1 Using UNION
- 11 INTERSECT 9
   - 11.1 Using INTERSECT
- 12 ActiveRecord 9
   - 12.1 Naive Queries
   - 12.2 Using #includes
   - 12.3 Manually selecting the needed fields from the database

## 1 LIMIT / OFFSET

LIMIT/OFFSET are commonly used for pagination. Here’s how you can use them.

### 1.1 Getting the first 10 results of a query

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
LIMIT 10;
```

### 1.2 Skipping the first 5 results, and getting the next

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
LIMIT 5
OFFSET 5;
```

## 2 ORDER BY

This allows you to sort your results by the values of any of the querie’s columns. It’s possible to sort in ascending and descending order as well as multiple columns at once. Here’s how to do it.

### 2.1 By default ORDER BY uses ASCending order:

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
ORDER BY first_name
LIMIT 10;
```

### 2.2 Sorting in DESCending order:

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
ORDER BY first_name DESC
LIMIT 10;
```

WTF?! The names are not correctly ordered!!!! That’s because lowercase letters are always sorted before uppercase. Read below to see how to circumvent this...

### 2.3 Sorting by the result of a function call on a column

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
ORDER BY lower(first_name) DESC
LIMIT 10;
```

### 2.4 Ordering by multiple columns

Order by first name DESCending, last name ASCending

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
ORDER BY lower(first_name) DESC, lower(last_name) ASC
LIMIT 10;
```

Order by first name DESCending, last name DESCending

```sql
SELECT first_name || ’ ’ || last_name
FROM patients
ORDER BY lower(first_name) DESC, lower(last_name) DESC
LIMIT 10;
```

## 3 JOIN

Used to get data from 2 tables where a certain condition matches both tables.


### 3.1 Getting data from 2 tables using JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
JOIN sites s ON ps.site_id = s.id
WHERE p.id = 1501;
```

## 4 LEFT JOIN

Used to get data from 2 tables where a certain condition matches at least on the first table.

### 4.1 Trying with a regular JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
JOIN sites s ON ps.site_id = s.id
WHERE p.id = 1300;
```

The result for this case will be empty, since there are no sites for patient 1300.

### 4.2 Trying with LEFT JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
LEFT JOIN patients_sites ps ON ps.patient_id = p.id
LEFT JOIN sites s ON ps.site_id = s.id
WHERE p.id = 1300;
```

In this case we can get results from the first table even when there are no matches.

## 5 RIGHT JOIN

Used to get data from 2 tables where a certain condition matches at least on the second table.


### 5.1 Trying with a regular JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
JOIN sites s ON ps.site_id = s.id
WHERE s.id = 2;
```

Using a regular JOIN will give us an empty result.

### 5.2 Trying with a RIGHT JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
RIGHT JOIN patients_sites ps ON ps.patient_id = p.id
RIGHT JOIN sites s ON ps.site_id = s.id
WHERE s.id = 2;
```

But with the RIGHT JOIN we can get results from the second table even when there are no matches.

## 6 FULL JOIN

Used to get data from 2 tables where a certain condition matches at least one of the tables

### 6.1 Trying with a regular JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
JOIN sites s ON ps.site_id = s.id
WHERE s.id = 1 OR p.id = 1300;
```

This will give us an empty result.

### 6.2 Trying with a FULL JOIN

```sql
SELECT p.first_name AS patient_name, s.name AS site_name
FROM patients p
FULL JOIN patients_sites ps ON ps.patient_id = p.id
FULL JOIN sites s ON ps.site_id = s.id
WHERE s.id = 1 OR p.id = 1300;
```

But with the FULL JOIN we can get results from both tables even when there are no matches.

## 7 GROUP

### 7.1 Using GROUP with aggregation functions

```sql
SELECT count(p.id) AS num_patients,
array_agg(p.first_name || ’ ’ || p.last_name) AS patient_name,
s.name AS site_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
JOIN sites s ON ps.site_id = s.id
GROUP BY s.id
LIMIT 5;
```

Here we used count and arrayaggto get the count of patients in each group as well as gather an array of patient names in each group.

## 8 Sub-SELECTS

These are useful to refine queries where it’s not easy to just do a JOIN/AND.

### 8.1 Example: Getting all patients that don’t belong to a certain site

#### site 8.1 Example: Getting all patients that don’t belong to a certain

```sql
SELECT p.first_name || ’ ’ || p.last_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
WHERE site_id = 3;
```

Let’s try getting patients that don’t belong:

```sql
SELECT p.first_name || ’ ’ || p.last_name
FROM patients p
JOIN patients_sites ps ON ps.patient_id = p.id
WHERE site_id = 3;
```

... WRONG! Sub-SELECTs to the rescue!

```sql
SELECT p.first_name || ’ ’ || p.last_name
FROM patients p
WHERE p.id NOT IN (
SELECT distinct(patient_id)
FROM patients_sites
WHERE site_id = 3
);
```

Here we issued a sub-query that got all the ids of patients belonging to a site, and the outer query gets all patients whose id doesn’t belong to that list.

## 9 Indexes and text searching

Indexes can speed up queries when there are many rows and the query you are performing returns a small percentage of the total. They can also speed up stuff like ORDER BY and GROUP BY. Let’s take this example query:

```sql
SELECT str FROM rxnconso WHERE str ILIKE ’benadryl%’;
```

So slow!... How can we improve the runtime?

### 9.1 EXPLAIN ANALYZE

Use EXPLAIN ANALYZE to see why your queries are slow:

```sql
EXPLAIN ANALYZE SELECT str FROM rxnconso WHERE str ILIKE ’benadryl%’;
```

### 9.2 Creating an index

For case insensitive searches we should create the index on lower(column):

```sql
CREATE INDEX idx_rxnconso_on_lower_str ON rxnconso (lower(str));
```

### 9.3 Making sure your query uses the index

If you try to run the previous query you’ll realize it’s still slow. This is because it couldn’t use the index. For searching on an index on lower(column) we need to change our WHERE clause:

```sql
SELECT str FROM rxnconso WHERE lower(str) LIKE lower(’benadryl%’);
```

### 9.4 B-Tree index limitations

Unfortunately b-tree indexes (the default type) only support prefix and equality searches on strings. For numeric types they support equality and >, <, >=, and <= operations as well. The following query will be slow for example (note the % at the beginning of the string):

```sql
SELECT str FROM rxnconso WHERE lower(str) LIKE lower(’%benadryl%’);
```

### 9.5 What if I need to do other types of searches?

#### 9.5.1 Full-text indexes

These allow to search for matches anywhere in the text with good performance. Also have the possibility to match between feminine/masculine and singular/plural forms.

#### 9.5.2 Trigram indexes

These allow similarity searches, so they can be good for searching in the presence of typing errors.

# 10 UNION

- Allows you to "add" together the results of 2 queries
- Removes duplicates on rows that are present in both result sets
- Does not remove duplicates that were present on a single result set
- Both queries must return the same column names and types

## 10.1 Using UNION

```sql
SELECT * FROM patients WHERE id = 3
UNION
SELECT * FROM patients WHERE id = 77;
```

The result of this query would include both patients.


# 11 INTERSECT

- Returns rows that are present in both result sets
- Both queries must return the same column names and types

## 11.1 Using INTERSECT

```sql
SELECT * FROM patients WHERE id > 3
INTERSECT
SELECT * FROM patients WHERE id < 77;
```

The result of this query would have all patients with ids between 3 and 77.

# 12 ActiveRecord

We can make use of some of the previous ideas to improve the runtime of slow activerecord queries. One common issue is the case of N+1 queries. Although ActiveRecord provides the #includes method to help deal with the issue, the fact is that by using includes we are still performing more than 1 query, as well as instantiating a lot of ruby objects.

Let’s imagine we want to get a list of sites with their respective patients so that we can return a JSON response to the user with a format like:

```json
[
  {
    site_name: "HUC",
    patients: [ "Zé das Couves", "El Duderino" ]
  },
  {
    site_name: "Givemeyourmoney Clinic",
    patients: [ "Adolf Hitler", "David Hasselhoff", "Lady Gaga" ]
  }
]
```

Let’s see 3 different ways to perform the necessary query with AR and create the JSON.


### 12.1 Naive Queries

```ruby
Benchmark.measure do
  Site.all.map do |s|
    {
      site_name: s.name,
      patients: s.patients.map { |p| "#{p.first_name} #{p.last_name}" }
    }
  end
end
```

This takes around 9.7 seconds on my computer. Hardly adequade for a web request.

### 12.2 Using #includes

```ruby
Benchmark.measure do
  Site.includes(:patients).map do |s|
    {
      site_name: s.name,
      patients: s.patients.map { |p| "#{p.first_name} #{p.last_name}" }
    }
  end
end
```

This takes around 21.4 seconds on my computer. Even slower than the naive approach!
Even though #includes is commonly recommended to fix N+1 queries, where there are a lot of results, the building of objects and relationships that is done in ruby-land inside ActiveRecord has a lot of overhead...

### 12.3 Manually selecting the needed fields from the database:

```ruby
Benchmark.measure do
  Site.select("sites.name, array_agg(patients.first_name || ’ ’ || patients.last_name) AS patient_names")
      .joins(:patients)
      .group(’sites.id’)
      .map do |s|
    {
      site_name: s.name,
      patients: s.patient_names
    }
  end
end
```

This runs in 1 second flat on my computer. Much better!


