# Lateral Join

- suppose we want to write a query that fetches the last purchase (timestamp) for every store in the system

- is the following query valid ?

```sql
SELECT s.storeName, purchaseTime.soldAt
FROM store s JOIN (
    SELECT timestamp
    FROM purchases p
    WHERE p.storeId = s.storeId  -- ERROR: s is not known
    ORDER BY timestamp DESC
) purchaseTime
```

- in the `FROM` phase, each table is treated/constructed independently. so, we can reference other tables in `FROM` clause sub-query

> note that we can reference other tables in `WHERE` clause sub-query (usuall correlated sub-query), but not in `FROM` clause sub-query

- sub-queries in `SELECT` must return 1 scalar value like `SELECT orderId, (SELECT ...) as value`
- sub-queries in `WHERE` doesn't return rows at all, it acts as a predicate and used with operators (`EXISTS`, `IN`, `>`, `=`, `<`)

- **lateral join** bridges the gap. it lets you write sub-queries in the `FROM` clause that see the other (outer) tables rows, and can return multiple rows & columns

## Example

- Suppose we want each customer's most recent order (order_id and created_at), including customers who have no orders (LEFT semantics).

### Option A (LEFT JOIN LATERAL)

- runs the inner lookup per customer row, ordered by created_at DESC, stopping at LIMIT 1 per customer.

```sql
SELECT c.id, c.name, o.id AS latest_order_id, o.created_at
FROM Customer c LEFT JOIN LATERAL (
    SELECT id, created_at
    FROM Orders
    WHERE customer_id = c.id
    ORDER BY created_at
    LIMIT 1
) o ON true;
```

- how it executes ? runs the inner query for **every** customer row, and may use index (if any) to avoid sorting all orders

> key point 1: it's useful to use lateral join if the outer set (customers in our example) aren't large

- it could be extended to find the **TOP-N** rows per parent

### Option B (Window Functions)

- make a sorted window for every customer, then filter when `rn = 1`

```sql
WITH ranked_orders AS (
    SELECT
        c.id AS customer_id,
        c.name,
        o.id AS latest_order_id,
        o.created_at,
        ROW_NUMBER() OVER (
            PARTITION BY c.id
            ORDER BY o.created_at DESC
        ) AS rn
    FROM customers c
    LEFT JOIN orders o ON o.customer_id = c.id
)
SELECT customer_id, name, latest_order_id, created_at
FROM ranked_orders
WHERE rn = 1;
```

- how it executes ? joins the 2 tables, divide into windows, and sort

- it would be a great option if we need to process many orders (not just the last), or if we need window aggregations (`LAG`, `SUM`, ...)

- it's very bad when a customer has 100K order, and we rank all of them just to get the last

### Option C (Scalar Subquery)

```sql
SELECT
    c.id,
    c.name,
    (
        SELECT o.id
        FROM orders o
        WHERE o.customer_id = c.id
        ORDER BY o.created_at DESC
        LIMIT 1
    ) AS latest_order_id,
    (
        SELECT o.created_at
        FROM orders o
        WHERE o.customer_id = c.id
        ORDER BY o.created_at DESC
        LIMIT 1
    ) AS latest_order_date
FROM customers c;
```

- how it executes ? runs 2 subqueries per row (unless combined)

- it will work only if we want 1 row per winning outer row (TOP-1, not TOP-N)

- as you can see, the query is verbose, and confusing

---

## Conclusion

- use lateral join when you need early termination (TOP N), with the ability to get many inner rows per each outer row
