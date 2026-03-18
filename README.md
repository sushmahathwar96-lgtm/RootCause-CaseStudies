# Root Cause Analysis Case Studies (Logs + SQL)

This repository contains real-world scenarios demonstrating Root Cause Analysis (RCA) using logs and SQL queries to debug production issues.

---

##  Case Study 1: Order Stuck in Processing

**Issue:**
Orders were stuck in “Processing” after successful payment.

**Analysis:**
Logs showed a `NullPointerException` in the delivery service.

**SQL Validation:**

```sql
SELECT order_id, status 
FROM orders 
WHERE order_id = 45678;

SELECT * 
FROM delivery_details 
WHERE order_id = 45678;

SELECT address_id 
FROM orders 
WHERE order_id = 45678;
```

**Root Cause:**
Missing `address_id` prevented delivery assignment.

**Resolution:**

```sql
UPDATE orders 
SET address_id = 12345 
WHERE order_id = 45678;
```

---

## Case Study 2: API Failure Impacting Order Creation

**Issue:**
Users were unable to place orders.

**Analysis:**
Logs showed `500 Internal Server Error` from payment API.

**SQL Validation:**

```sql
SELECT * 
FROM orders 
WHERE user_id = 101 
ORDER BY created_at DESC;

SELECT * 
FROM payment_transactions 
WHERE user_id = 101;
```

**Root Cause:**
Payment API timeout issue.

**Resolution:**
Retry mechanism and proper error handling implemented.

---

##  Case Study 3: Login Failure Due to Database Issue

**Issue:**
Users unable to log in despite valid credentials.

**Analysis:**
Logs showed authentication failures.

**SQL Validation:**

```sql
SELECT user_id, status 
FROM users 
WHERE email = 'user@example.com';

SELECT * 
FROM users 
WHERE status NOT IN ('ACTIVE', 'INACTIVE');
```

**Root Cause:**
Invalid user status due to incorrect data migration.

**Resolution:**

```sql
UPDATE users 
SET status = 'ACTIVE' 
WHERE status IS NULL;
```

---

##  Case Study 4: Slow Application Performance

**Issue:**
Application dashboard was loading slowly.

**Analysis:**
Logs showed slow database query execution.

**SQL Validation:**

```sql
EXPLAIN ANALYZE 
SELECT * 
FROM orders 
WHERE user_id = 101;
```

**Root Cause:**
Missing index on `user_id`.

**Resolution:**

```sql
CREATE INDEX idx_user_id 
ON orders(user_id);
```

---

##  Case Study 5: Duplicate Orders Issue

**Issue:**
Duplicate orders were created.

**Analysis:**
Logs showed repeated API calls.

**SQL Validation:**

```sql
SELECT order_id, COUNT(*) 
FROM orders 
GROUP BY order_id 
HAVING COUNT(*) > 1;
```

**Root Cause:**
No idempotency handling in API.

**Resolution:**

```sql
ALTER TABLE orders 
ADD CONSTRAINT unique_order UNIQUE(order_id);
```



