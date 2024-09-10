# Market-Micro - E-commerce Backend in Microservices Architecture

## Project Overview

Market-Micro is a microservices-based backend system for a single-vendor e-commerce platform. It uses a modular, scalable approach to deliver efficient and high-performance service. The project implements user authentication, product management, shopping cart operations, order processing, and an admin dashboard. Elasticsearch powers advanced search capabilities, while Redis is used for caching to optimize performance.

---

## Table of Contents

1. Features
2. Microservices Overview
3. Technology Stack
4. Architecture
5. Installation and Setup
6. API Documentation
7. Scaling and Performance
8. Future Enhancements

---

## Features

-   **User Management:** Secure user registration, login, and authentication with JWT.
-   **Product Management:** CRUD operations for products with advanced search using Elasticsearch.
-   **Shopping Cart:** Session-based cart that tracks items for logged-in users.
-   **Order Processing:** Create, track, and manage orders.
-   **Admin Dashboard:** Manage products, view financial data, and handle inventory.

---

## Microservices Overview

Each microservice is developed independently, communicates via RabbitMQ for event-driven actions, and scales according to traffic and usage needs. Below is a detailed description of each microservice.

### 1. User Microservice

-   **Responsibilities:**
    -   User registration, authentication (JWT-based), and profile management.
    -   Password hashing and secure authentication.
    -   Handles session management using Redis for fast access to user data.
-   **Endpoints:**
    -   `POST /api/v1/users/register` – Register a new user.
    -   `POST /api/v1/users/login` – Authenticate a user and return JWT.
    -   `GET /api/v1/users/profile` – Retrieve user profile details (authentication required).
-   **Technology:**
    -   Django, MySQL, Redis, RabbitMQ.

### 2. Product Microservice

-   **Responsibilities:**
    -   Manage product listings, categories, and prices.
    -   Elasticsearch integration for product search (text, category, and filters).
-   **Endpoints:**
    -   `POST /api/v1/products` – Create a new product.
    -   `GET /api/v1/products` – List products with search and filters.
    -   `GET /api/v1/products/{id}` – Retrieve product details.
    -   `PUT /api/v1/products/{id}` – Update a product.
    -   `DELETE /api/v1/products/{id}` – Remove a product.
-   **Technology:**
    -   Django, MySQL, Elasticsearch, RabbitMQ.

### 3. Cart Microservice

-   **Responsibilities:**
    -   Manage shopping cart operations such as adding, removing, and updating items.
    -   Session-based cart for authenticated users.
    -   Data is stored temporarily in Redis for quick access.
-   **Endpoints:**
    -   `POST /api/v1/cart/add` – Add product to cart.
    -   `GET /api/v1/cart` – Get cart details for current user.
    -   `PUT /api/v1/cart/update/{productId}` – Update item quantity in the cart.
    -   `DELETE /api/v1/cart/remove/{productId}` – Remove item from the cart.
-   **Technology:**
    -   Django, Redis, RabbitMQ.

### 4. Order Microservice

-   **Responsibilities:**
    -   Process orders, update statuses, and manage order history.
    -   Sync with the Inventory microservice to adjust stock after orders.
    -   Handles the entire order lifecycle: placed, confirmed, shipped, delivered.
-   **Endpoints:**
    -   `POST /api/v1/orders` – Place an order.
    -   `GET /api/v1/orders/{id}` – Get details of a specific order.
    -   `PUT /api/v1/orders/{id}/status` – Update order status (admin only).
-   **Technology:**
    -   Django, MySQL, RabbitMQ.

### 5. Admin Microservice

-   **Responsibilities:**
    -   Handle administrative tasks for managing products, such as adding, updating, and deleting products.
    -   Admins can create, update, or delete products and adjust inventory.
    -   Retrieve financial insights from sales and order data.
-   **Endpoints:**
    -   `POST /api/v1/admin/products` – Create a new product and get products list (admin only).
    -   `PUT /api/v1/admin/products/{id}` – Update product details.
    -   `DELETE /api/v1/admin/products/{id}` – Delete a product.
    -   `GET /api/v1/admin/financials` – View sales and financial summaries.
-   **Technology:**
    -   Django, MySQL, RabbitMQ.

---

## Technology Stack

-   **Backend Framework:** Django
-   **Database:** MySQL for relational data
-   **Cache:** Redis for caching session data and cart information
-   **Message Broker:** RabbitMQ for event-driven microservice communication
-   **Search Engine:** Elasticsearch for advanced product search functionality
-   **API Gateway:** NGINX for routing and load balancing
-   **Containerization:** Docker and Docker Compose for microservice orchestration

---

## Architecture

## The architecture follows a microservices-based approach, enabling the independent scaling and deployment of each service. Each microservice communicates with others asynchronously via RabbitMQ, and services are exposed through an API gateway (NGINX). Key data flows are optimized using Redis caching and Elasticsearch for search performance.

## Installation and Setup

#### 1. Clone the Repository

```
   git clone https://github.com/yourusername/market-micro.git
   cd market-micro
```

#### 2. Docker Setup

Ensure Docker and Docker Compose are installed. Then, start all services:

```
docker-compose up --build
```

#### 3. Environment Variables

Create .env files for each microservice with the necessary environment variables (e.g., MySQL, Redis, RabbitMQ credentials). Example .env:

```
DATABASE_URL=mysql://user:password@db:3306/market_micro
REDIS_URL=redis://localhost:6379/0
RABBITMQ_URL=amqp://user:password@rabbitmq
ELASTICSEARCH_URL=http://localhost:9200
```

#### 4. Apply Database Migrations

Run migrations for each microservice:

```
docker-compose exec user-service python manage.py migrate
docker-compose exec product-service python manage.py migrate
```

---

### API Documentation

#### User Microservice

-   `POST /api/v1/users/register` – Register a new user.
-   `POST /api/v1/users/login` – User authentication.
-   `GET /api/v1/users/profile` – Retrieve user profile.
-   `GET /api/v1/users/me` – Validate user using access token.
-   `POST /api/v1/users/refresh` – Refresh access token using resfresh token.

#### Product Microservice

-   `GET /api/v1/products` – List/search products.
-   `GET /api/v1/products/{id}` – Product details.

#### Cart Microservice

-   `POST /api/v1/cart/add` – Add product to cart.
-   `GET /api/v1/cart` – Get current cart.

#### Order Microservice

-   `POST /api/v1/orders` – Place an order.
-   `GET /api/v1/orders/{id}` – Get order details.

#### Admin Microservice

-   `GET /api/v1/admin/orders` – Retrieve a list of all orders along with financial summaries.
-   `POST /api/v1/admin/products` - Add a new product to the catalog.
-   `PUT /api/v1/admin/products/{id}` - Update details of an existing product.
-   `DELETE /api/v1/admin/products/{id}` - Remove a product from the catalog.
-   `GET /api/v1/admin/financials` - Get financial summaries, including sales and revenue data.

---

## Scaling and Performance

-   **Redis Caching:** Speeds up cart retrieval and user session management.
-   **Elasticsearch:** Optimizes search performance with full-text search and filtering.
-   **RabbitMQ:** Ensures reliable, asynchronous communication between microservices, decoupling them and allowing independent scaling.
-   **Docker:** Services can be scaled individually based on demand using Docker Compose.

---

## Future Enhancements

-   **Payment Microservice:** Integrate payment processing for order checkout.
-   **Review Microservice:** Allow users to leave reviews on products.
-   **Notification Microservice:** Notify users via email or SMS for order status updates, promotions, etc.
-   **Analytics Microservice:** Analyze sales trends and user activity for better decision-making.
-   **Inventory Microservice Enhancements:** Expand inventory management with batch processing, automatic stock updates, and stock predictions based on sales patterns.
