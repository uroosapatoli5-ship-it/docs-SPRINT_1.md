Sprint 1: Architecture & Scope Definition E-Commerce Platform
Student Roll No: [2k25/TYCS/2]
Repo: ecommerce-[ 2k25/TYCS/2]
Date: September 13, 2026
Sprint Goal: Define architecture, scope, and data model for MVP.

Target Audience & Market Focus
Market Vertical: D2C Fashion & Streetwear E-commerce (Pakistan)
1. Target Audience & Market Focus
Primary Persona
Name: Sarah Ahmed
Age: 28
Occupation: Small Business Owner (Handmade Jewelry & Accessories)
Location: Urban metropolitan area
Tech Proficiency: Moderate — comfortable with smartphones, social media, and basic e-commerce platforms (Etsy, Daraz).

Behavioral Characteristics:

Shops online 2–3 times per month for personal and business needs.

Prefers mobile-friendly interfaces with fast checkout.

Values product authenticity, clear pricing, and reliable delivery estimates.

Abandons carts when the checkout process is lengthy or unclear.

Core Pain Point
Small-scale artisans and local sellers lack an affordable, customizable, and scalable digital storefront that supports inventory management, secure payments, and order tracking without requiring deep technical expertise. Existing platforms charge high commissions, limit branding control, and provide poor analytics for inventory decisions.

Domain Scope
Vertical Market: Consumer Electronics & Accessories (with extensibility to Apparel, Handmade Goods, and Digital Assets)
The platform is designed as a general-purpose e-commerce solution but will be initially scoped, seeded, and validated using the Consumer Electronics & Accessories vertical. This vertical was chosen because: Products have well-defined attributes (brand, model, warranty, specifications).
Inventory management is critical due to stock sensitivity.
High transaction volume and repeat purchase behavior.
Clear taxonomy requirements (categories → subcategories → brands).
The architecture will remain domain-agnostic at the database level to allow future expansion into other verticals without schema redesign.
Minimum Viable Product (MVP) Feature Scope
The MVP comprises five primary user workflows that are feasible within the academic semester timeline. Each feature is prioritized based on user value and technical dependency.






Table 1: MVP Feature Scope Matrix
Category	Feature Name	Description	Priority
Authentication	User Registration & Authentication	Secure signup/login using email and password. Passwords hashed with bcrypt. JWT-based session tokens for stateless authentication. Role-based access (Customer/Admin).	High (MVP)
Catalog	Product List & Search	Paginated product browsing with taxonomy-based filtering (category, brand, price range). Full-text search on product name and description. Sort by price, popularity, and decency.	High (MVP)
Cart	Cart Management	State-persistent cart for authenticated users (database-backed) and session-based cart for guests. Add, update quantity, remove items. Real-time subtotal calculation.	High (MVP)
Checkout	Order Processing	Mock payment gateway integration (Stripe sandbox). Order object instantiation with line items, shipping address, and status tracking. Email confirmation (mock/SMTP).	High (MVP)
Admin	Inventory Control	Administrative CRUD operations for products, categories, and stock levels. Low-stock alerts. Order status management (Pending → Shipped → Delivered).	Medium (MVP)
User Profile	Order History & Profile	Authenticated users can view past orders, track status, and manage shipping addresses.	Medium (MVP)

Scope Bounding Notes:

No real-time chat, recommendations engine, or multi-vendor support in MVP.

Payment is sandboxed (no real transactions).

Email notifications are mocked or use a free SMTP tier.

Mobile responsiveness is required but native apps are out of scope
Tech Stack Selection & Justification
Frontend Framework: React.js (with Vite)
Justification: React provides a component-based architecture ideal for reusable UI elements (product cards, cart widgets, forms). Its vast ecosystem (React Router, Axios, Context API) accelerates development. Vite offers faster build times and hot module replacement compared to Create React App. Alternative considered: Next.js — rejected because SSR/SSG adds complexity not needed for an MVP SPA, and the team has stronger React fundamentals.

Backend Infrastructure: Node.js with Express.js
Justification: Express is lightweight, unopinionated, and allows rapid REST API development with middleware for JWT auth, validation, and error handling. Node’s non-blocking I/O suits an e-commerce catalog with concurrent read requests. Alternative considered: Django (Python) — rejected due to heavier ORM abstraction and slower iteration for a small team; Spring Boot — rejected due to boilerplate overhead and steeper learning curve.

Database Management System: PostgreSQL
Justification: PostgreSQL offers ACID compliance, strong relational integrity, and advanced features (JSONB for flexible product attributes, full-text search, window functions). The e-commerce domain requires strict foreign key constraints and transactional consistency for orders and inventory. Alternative considered: MongoDB — rejected because order/cart relationships are inherently relational, and eventual consistency risks overselling inventory.

Caching & Asynchronous Processing (Optional): Redis
Justification: Redis will be used for session token blacklisting (logout), caching product listings for high-traffic endpoints, and rate-limiting authentication attempts. Its in-memory speed reduces database load for read-heavy catalog queries. Alternative considered: Memcached — rejected due to lack of persistence and data structure flexibility.
Deployment & DevOps (Supporting Stack)
Layer	Technology	Purpose
Hosting (Frontend)	Vercel / Netlify	Static SPA hosting with CI/CD
Hosting (Backend)	Render / Railway	Node.js API hosting with environment variables
Database Hosting	Supabase / Neon	Managed PostgreSQL with backups
Version Control	GitHub	Source code, PR reviews, sprint documentation
API Testing	Postman / Thunder Client	Endpoint validation during development




4. Entity-Relationship Diagram (ERD)
4.1 Entity Definitions & Attributes
USERS
Attribute	Type	Constraint	Description
Id	SERIAL	PK	Unique user identifier
Email	VARCHAR(255)	UNIQUE, NOT NULL	Login email
Password_hash	VARCHAR(255)	NOT NULL	Bcrypt-hashed password
Full_name	VARCHAR(150)	NOT NULL	Display name
Role	VARCHAR(20)	DEFAULT ‘customer’	‘customer’ or ‘admin’
Created_at	TIMESTAMP	DEFAULT NOW()	Account creation time


CATEGORIES
Attribute	Type	Constraint	Description
Id	SERIAL	PK	Unique category identifier
Name	VARCHAR(100)	NOT NULL	Category name
Slug	VARCHAR(120)	UNIQUE, NOT NULL	URL-friendly identifier
Parent_id	INTEGER	FK → CATEGORIES(id)	Self-referencing for subcategories

PRODUCTS
Attribute	Type	Constraint	Description
Id	SERIAL	PK	Unique product identifier
Category ID	INTEGER	FK → CATEGORIES(id)	Product category
Name	VARCHAR(200)	NOT NULL	Product name
Description	TEXT		Detailed description
Price	DECIMAL(10,2)	NOT NULL, CHECK > 0	Unit price
Stock_quantity	INTEGER	NOT NULL, DEFAULT 0	Available inventory
Image_url	VARCHAR(500)		Product image path
Created_at	TIMESTAMP	DEFAULT NOW()	Listing creation time



ORDERS
Attribute	Type	Constraint	Description
Id	SERIAL	PK	Unique order identifier
User_id	INTEGER	FK → USERS(id)	Ordering user
Total_amount	DECIMAL(10,2)	NOT NULL	Order total
Status	VARCHAR(30)	DEFAULT ‘pending’	pending/shipped/delivered/cancelled
Shipping_address	TEXT	NOT NULL	Delivery address
Created_at	TIMESTAMP	DEFAULT NOW()	Order placement time





ORDER_ITEMS (Associative Entity)
Attribute	Type	Constraint	Description
Id	SERIAL	PK	Unique line item identifier
Order_id	INTEGER	FK → ORDERS(id)	Parent order
Product_id	INTEGER	FK → PRODUCTS(id)	Ordered product
Quantity	INTEGER	NOT NULL, CHECK > 0	Quantity ordered
Unit_price	DECIMAL(10,2)	NOT NULL	Price at time of order














4.2 Cardinality & Relationships
Relationship	Cardinality	Description
USERS → ORDERS	1:N	One user places many orders; each order belongs to one user
USERS → CART	1:1	One user has one active cart
CART → CART_ITEMS	1:N	One cart contains many items
PRODUCTS → CART_ITEMS	1:N	One product can appear in many cart items
ORDERS → ORDER_ITEMS	1:N	One order contains many line items
PRODUCTS → ORDER_ITEMS	1:N	One product can appear in many order items
CATEGORIES → PRODUCTS	1:N	One category contains many products
CATEGORIES → CATEGORIES	1:N	Self-referencing parent-child hierarchy








4.3 Mermaid ERD







4.4 Data Normalization Notes
1NF: All attributes are atomic; no repeating groups.

2NF: All non-key attributes fully depend on their respective primary keys (e.g., unit_price in ORDER_ITEMS depends on the composite relationship but is stored per line item to preserve historical pricing).

3NF: No transitive dependencies. Unit_price is intentionally demoralized in ORDER_ITEMS to capture price at time of purchase (historical accuracy), which is a deliberate exception for auditability.

Referential Integrity: ON DELETE CASCADE for cart items; ON DELETE RESTRICT for orders to preserve transaction history.









Repository Structure                                                                                                                                                              /ecommerce-project
├── /docs
│   └── SPRINT_1.md
├── /frontend          (React + Vite)
├── /backend           (Node.js + Express)
├── /database
│   └── schema.sql
├── .gitignore
└── README.md









Submission Checklist
☑ GitHub repository initialized
☑ Instructor & TA granted collaborator access
☑ SPRINT_1.md committed to /docs directory
☑ Mermaid ERD renders natively in Markdown
☑ All four required sections completed
☑ Repository URL submitted to LMS
End of Sprint 1 Documentation



