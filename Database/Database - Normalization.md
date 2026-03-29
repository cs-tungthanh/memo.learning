---
tags: Normalization
---
**Normalization**: break data into multiple tables -> reduce redundancy but need more join
**Denormalization**: merge data into one table -> speed up read query

Example:
we have a table with: order_id, product_id, product_name, user_info, city, zip_code
PK(order_id, product_id)

1NF: violate user_info -> need to split into user table and ref by userID
2NF violate order_id + product_name
	-> in order table just need to store order_id, product_id, user_id, quantity is enough
	product_name is depend on product_id not primary key of this table like (order_id, product_id) only depend on partial of PK in this table
3NF: from **city** we can derive **zip code** -> split zip code into other table
	- diff with 2NF: city is not in keys of table

### Transitive dependency
> (A → B) **AND** (B → C) ⇒ (A → C)

**Normalization** is used to **minimize redundancy** and dependency by organizing fields and table of a database.
- First normal form (1NF): value is **atomic** not metadata like **userInfo**: name,phone,.. in a same column
- Second normal form (2NF)
	- Must be in 1NF
	- xuất hiện khi **non-key depends partially on candidate keys**
- Third normal form(3NF)
	- Must be in 2NF
	- Don’t have a transitive dependency
- Boyce-Codd normal form(BCNF)

### Detail example:
![](./assets/normalize-violated-3NF.png)

`{Std_Id, subject_Id}`: Compositive key
It has violated **3NF** because there’s a transitive dependency

`(Std_Id, Subject_Id)` -> `Exam_type`
`Exam_type -> Toltal_Mark`
—> Resolve 3NF
![](./assets/normalize-resolved-3NF.png)

Assuming `Toltal_Mark` depends on `(Std_Id, Subject_Id)` ->No need to normalize
Assuming `Toltal_Mark` depends on `(Std_Id)` -> It has violated **2NF**


# Database Normalization vs. Denormalization

## Normalization 
*Breaking data into multiple tables to reduce redundancy*

✅ **Pros:**
- Eliminates data duplication
- Ensures data integrity
- Simplifies updates (change once, update everywhere)
- Saves storage space

❌ **Cons:**
- Requires multiple JOINs → slower queries
- More complex SQL for retrieving complete information

## Denormalization 🚀
*Combining tables or duplicating data to improve read performance*

✅ **Pros:**
- Faster queries (fewer or no JOINs)
- Simpler SQL statements
- Better read performance

❌ **Cons:**
- Data duplication
- Update challenges (must change data in multiple places)
- Data inconsistency risks
- Higher storage requirements

## Quick Example

### Normalized (4 Tables):
```
orders: id, user_id, created_at
users: id, name, address
products: id, product_name, price
order_items: order_id, product_id, quantity
```

**Query needs 4 JOINs!** 👇
```sql
SELECT o.id, u.name, p.product_name, oi.quantity
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
```

### Denormalized (1 Table):
```
orders: id, user_name, user_address, created_at, products_json
```

**Simple query:** `SELECT * FROM orders`

## Best Practice: Hybrid Approach 🔄

### Write Operations → Use Normalized Schema
- Maintain data integrity
- Prevent anomalies

### Read Operations → Use Denormalized Views
- Materialized views
- Caching layer
- Read replicas

**Remember:** Normalize for writes, denormalize for reads!

