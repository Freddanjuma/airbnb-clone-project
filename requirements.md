Airbnb Clone – Database Specification & ER Diagram Requirements

This document defines the **Entity-Relationship Diagram (ERD)** and database schema for the Airbnb Clone project.  
It includes all entities, attributes, data types, constraints, and relationships forming the backbone of the backend database.

---

## 🧩 Entities and Attributes

### 👤 User

**Description:**  
Represents all users (guests, hosts, and admins) who interact with the system.

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `user_id` | UUID | Primary Key, Indexed | Unique user identifier |
| `first_name` | VARCHAR | NOT NULL | User's first name |
| `last_name` | VARCHAR | NOT NULL | User's last name |
| `email` | VARCHAR | UNIQUE, NOT NULL | User’s login email |
| `password_hash` | VARCHAR | NOT NULL | Hashed password |
| `phone_number` | VARCHAR | NULL | Optional phone number |
| `role` | ENUM(`guest`, `host`, `admin`) | NOT NULL | Defines user role |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Account creation timestamp |

---

### 🏠 Property

**Description:**  
Represents accommodation listings created by hosts.

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `property_id` | UUID | Primary Key, Indexed | Unique property identifier |
| `host_id` | UUID | Foreign Key → User(user_id) | Links property to host |
| `name` | VARCHAR | NOT NULL | Property title |
| `description` | TEXT | NOT NULL | Full description of the property |
| `location` | VARCHAR | NOT NULL | Physical address or location |
| `price_per_night` | DECIMAL | NOT NULL | Cost per night stay |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Date created |
| `updated_at` | TIMESTAMP | ON UPDATE CURRENT_TIMESTAMP | Last update timestamp |

---

### 📅 Booking

**Description:**  
Represents reservations made by guests for properties.

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `booking_id` | UUID | Primary Key, Indexed | Unique booking identifier |
| `property_id` | UUID | Foreign Key → Property(property_id) | The booked property |
| `user_id` | UUID | Foreign Key → User(user_id) | Guest who made the booking |
| `start_date` | DATE | NOT NULL | Check-in date |
| `end_date` | DATE | NOT NULL | Check-out date |
| `total_price` | DECIMAL | NOT NULL | Total cost for the stay |
| `status` | ENUM(`pending`, `confirmed`, `canceled`) | NOT NULL | Booking status |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Booking creation time |

---

### 💳 Payment

**Description:**  
Handles payments for bookings.

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `payment_id` | UUID | Primary Key, Indexed | Unique payment ID |
| `booking_id` | UUID | Foreign Key → Booking(booking_id) | Related booking |
| `amount` | DECIMAL | NOT NULL | Amount paid |
| `payment_date` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Payment timestamp |
| `payment_method` | ENUM(`credit_card`, `paypal`, `stripe`) | NOT NULL | Payment channel used |

---

### 💬 Review

**Description:**  
Represents guest feedback on properties after stays.

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `review_id` | UUID | Primary Key, Indexed | Unique review identifier |
| `property_id` | UUID | Foreign Key → Property(property_id) | Reviewed property |
| `user_id` | UUID | Foreign Key → User(user_id) | Reviewer (guest) |
| `rating` | INTEGER | CHECK(rating >= 1 AND rating <= 5), NOT NULL | Star rating (1–5) |
| `comment` | TEXT | NOT NULL | Review text |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Review creation timestamp |

---

### 💌 Message

**Description:**  
Stores messages exchanged between users (e.g., guest ↔ host).

| Field | Type | Constraints | Description |
|--------|------|-------------|--------------|
| `message_id` | UUID | Primary Key, Indexed | Unique message identifier |
| `sender_id` | UUID | Foreign Key → User(user_id) | User sending the message |
| `recipient_id` | UUID | Foreign Key → User(user_id) | User receiving the message |
| `message_body` | TEXT | NOT NULL | Content of the message |
| `sent_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Message timestamp |

---

## 🔗 Relationships

| Relationship | Type | Description |
|---------------|------|-------------|
| **User → Property** | 1 : N | A host can list multiple properties |
| **User → Booking** | 1 : N | A guest can make multiple bookings |
| **Property → Booking** | 1 : N | A property can have many bookings |
| **Booking → Payment** | 1 : 1 | Each booking has one payment |
| **Property → Review** | 1 : N | A property can have multiple reviews |
| **User → Review** | 1 : N | A guest can write multiple reviews |
| **User → Message (Sender)** | 1 : N | A user can send many messages |
| **User → Message (Recipient)** | 1 : N | A user can receive many messages |

---

## 🧭 ER Diagram Layout (Suggested)

To visualize:
1. **User** at top-center — main entity.
2. **Property** below User → linked via host_id.
3. **Booking** connected between Property and User.
4. **Payment** right side of Booking (1:1 relation).
5. **Review** under Property (1:N).
6. **Message** below User (two relations: sender and recipient).

Use **Crow’s Foot Notation** for cardinalities (1, N, 1:1).

---

## ⚙️ Implementation Notes

- Use `UUID` for all primary keys (scalable and unique across distributed systems).  
- Apply **foreign key constraints** to enforce relational integrity.  
- Add indexes on frequently searched columns (`email`, `property_id`, `booking_id`).  
- Use **ENUMs** for role, status, and payment method to maintain data consistency.  
- Add **timestamps** for auditing and historical tracking.

---

# Airbnb Database Entity-Relationship Diagram

Below is the ERD illustrating the relationships between the key entities:  
**User, Property, Booking, Payment, Review, and Message**.

Diagram link: (https://app.diagrams.net/#G1PkhzeuCNkSLw8BqdR_CBuB7BzeKz2t4j#%7B%22pageId%22%3A%22yf9Qftj7pLVsSTz_qJ0F%22%7D)

---

### Entities Overview

**User**
- user_id (PK)
- first_name
- last_name
- email
- password_hash
- role

**Property**
- property_id (PK)
- host_id (FK → User.user_id)
- name
- description
- price_per_night
- location

**Booking**
- booking_id (PK)
- property_id (FK → Property.property_id)
- user_id (FK → User.user_id)
- start_date
- end_date
- total_price

**Payment**
- payment_id (PK)
- booking_id (FK → Booking.booking_id)
- amount
- payment_date
- payment_method

**Review**
- review_id (PK)
- property_id (FK → Property.property_id)
- user_id (FK → User.user_id)
- rating
- comment

**Message**
- message_id (PK)
- sender_id (FK → User.user_id)
- recipient_id (FK → User.user_id)
- message_body
- sent_at
