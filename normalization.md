# 🧮 Database Normalization – Airbnb Clone Project

This document explains the normalization process applied to the **Airbnb Clone Project** database design to ensure the schema complies with **Third Normal Form (3NF)**.

---

## ⚙️ Step 1: Understanding Normalization

**Database Normalization** is the process of organizing data to reduce redundancy and improve data integrity.  
Each normal form builds on the previous one:

1. **1NF (First Normal Form)** – Eliminate repeating groups; ensure each cell contains only atomic (indivisible) values.
2. **2NF (Second Normal Form)** – Remove partial dependencies; every non-key attribute must depend on the whole primary key.
3. **3NF (Third Normal Form)** – Remove transitive dependencies; non-key attributes must depend only on the primary key.

---

## 🧩 Step 2: Review of Entities and Attributes

Our main entities are:

- **User**
- **Property**
- **Booking**
- **Payment**
- **Review**
- **Message**

Each entity already follows the **1NF rule** (atomic columns, unique primary keys). Let’s examine normalization further.

---

## 🔍 Step 3: 1NF → 2NF Conversion

All entities have **single-column primary keys (UUID)**.  
This automatically satisfies **2NF**, since there are no composite primary keys that could cause partial dependencies.

✅ **No changes were required** between 1NF and 2NF.

---

## 🔐 Step 4: 2NF → 3NF Conversion

We check for **transitive dependencies** — where a non-key field depends on another non-key field instead of the primary key.

### Entity Analysis:

#### 🧑‍💼 User
| Field | Dependency | Comment |
|--------|-------------|----------|
| email → password_hash | ❌ Not dependent (valid, unique to user) |
| role depends only on user_id | ✅ OK |
**✅ User table is in 3NF.**

---

#### 🏠 Property
| Field | Dependency | Comment |
|--------|-------------|----------|
| host_id → host (User.user_id) | ✅ Valid FK |
| price_per_night only depends on property_id | ✅ OK |
**✅ Property table is in 3NF.**

---

#### 📅 Booking
| Field | Dependency | Comment |
|--------|-------------|----------|
| total_price = (price_per_night × days) | ⚠️ Derived field — can cause redundancy |
**Solution:** Remove `total_price` as a stored field and calculate it dynamically when needed.  

✅ After removing this derived value, Booking is in **3NF**.

---

#### 💳 Payment
| Field | Dependency | Comment |
|--------|-------------|----------|
| payment_method depends only on payment_id | ✅ OK |
| amount depends on booking_id | ✅ OK |
**✅ Payment table is in 3NF.**

---

#### ⭐ Review
| Field | Dependency | Comment |
|--------|-------------|----------|
| rating depends only on review_id | ✅ OK |
| property_id and user_id are valid FKs | ✅ OK |
**✅ Review table is in 3NF.**

---

#### 💬 Message
| Field | Dependency | Comment |
|--------|-------------|----------|
| sender_id and recipient_id both reference users | ✅ OK |
| message_body depends only on message_id | ✅ OK |
**✅ Message table is in 3NF.**

---

## 🧾 Step 5: Final Normalized Structure Summary

After normalization, the database achieves **Third Normal Form (3NF)**:
- Each entity has atomic attributes (1NF)
- All attributes depend on the full primary key (2NF)
- No transitive dependencies remain (3NF)
- Derived fields like `total_price` are computed dynamically instead of stored.

---

## ✅ Conclusion

The **Airbnb Clone Project** database design is fully normalized to **3NF**.  
This ensures:
- Minimal redundancy  
- Strong data integrity  
- Efficient updates and scalability  

---

### Example Improvement:
Instead of storing `total_price` in **Booking**, compute it as:

```sql
SELECT 
    b.booking_id,
    p.price_per_night * (b.end_date - b.start_date) AS total_price
FROM 
    Booking b
JOIN 
    Property p ON b.property_id = p.property_id;
