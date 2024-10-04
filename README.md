Here is a comprehensive README template for each of the services (User, Product, Order) and the GraphQL Gateway. The following setup assumes each service has its own repository, and you will use RabbitMQ for event handling.

---

# **User Service - README**

## **Overview**

The User Service is responsible for handling user registration, authentication, and user profile management. It exposes a RESTful API for these functionalities and communicates with other services via RabbitMQ for event-driven updates.

## **Features**

- User registration with hashed passwords.
- User authentication using JWT tokens.
- Profile update functionality for logged-in users.
- Event-driven architecture using RabbitMQ.

## **Technologies**

- **Node.js** with **Express**
- **MongoDB** for database
- **RabbitMQ** for event-driven communication
- **JWT** for authentication

## **Setup Instructions**

1. **Clone the repository:**

```bash
git clone <repository-url>
cd user-service
```

2. **Install dependencies:**

```bash
npm install
```

3. **Set up environment variables:**

Create a `.env` file in the root directory with the following variables:

```
PORT=3005
MONGODB_URI=mongodb://localhost:27017/userdb
JWT_SECRET=your_jwt_secret
RABBITMQ_URL=amqp://localhost
```

4. **Run MongoDB and RabbitMQ:**

Ensure MongoDB and RabbitMQ are running locally or via Docker:

```bash
docker run -d -p 27017:27017 --name mongodb mongo
docker run -d -p 5672:5672 -p 15672:15672 --name rabbitmq rabbitmq:3-management
```

5. **Run the service:**

```bash
npm run dev
```

6. **API Endpoints:**

- `POST /api/users/register` - Register a new user.
- `POST /api/users/login` - Authenticate a user.
- `PUT /api/users/profile` - Update user profile (JWT token required).

7. **Testing:**

Use Postman to test the API or write automated tests with tools like Jest.

---

# **Product Service - README**

## **Overview**

The Product Service is responsible for managing products in the system. It handles CRUD operations for products and updates inventory based on events from the Order Service.

## **Features**

- CRUD operations for products.
- Event-driven inventory updates.
- Admin-protected routes for product creation and updates.

## **Technologies**

- **Node.js** with **Express**
- **MongoDB** for database
- **RabbitMQ** for event-driven communication
- **JWT** for admin authorization

## **Setup Instructions**

1. **Clone the repository:**

```bash
git clone <repository-url>
cd product-service
```

2. **Install dependencies:**

```bash
npm install
```

3. **Set up environment variables:**

Create a `.env` file in the root directory with the following variables:

```
PORT=3006
MONGODB_URI=mongodb://localhost:27017/productdb
JWT_SECRET=your_jwt_secret
RABBITMQ_URL=amqp://localhost
```

4. **Run MongoDB and RabbitMQ:**

Ensure MongoDB and RabbitMQ are running locally or via Docker.

5. **Run the service:**

```bash
npm run dev
```

6. **API Endpoints:**

- `POST /api/products` - Create a new product (Admin only).
- `GET /api/products/:id` - Fetch a product by ID.
- `PUT /api/products/:id` - Update product details (Admin only).

7. **Testing:**

Use Postman or any other API testing tool to test the endpoints.

---

# **Order Service - README**

## **Overview**

The Order Service handles order creation and manages inventory based on the products selected. It processes orders and communicates with the Product Service to update product inventory using RabbitMQ.

## **Features**

- Order placement by authenticated users.
- Inventory management based on product availability.
- Event-driven architecture using RabbitMQ.

## **Technologies**

- **Node.js** with **Express**
- **MongoDB** for database
- **RabbitMQ** for event-driven communication
- **JWT** for user authentication

## **Setup Instructions**

1. **Clone the repository:**

```bash
git clone <repository-url>
cd order-service
```

2. **Install dependencies:**

```bash
npm install
```

3. **Set up environment variables:**

Create a `.env` file in the root directory with the following variables:

```
PORT=3002
MONGODB_URI=mongodb://localhost:27017/orderdb
JWT_SECRET=your_jwt_secret
RABBITMQ_URL=amqp://localhost
```

4. **Run MongoDB and RabbitMQ:**

Ensure MongoDB and RabbitMQ are running locally or via Docker.

5. **Run the service:**

```bash
npm run dev
```

6. **API Endpoints:**

- `POST /api/orders` - Place a new order (User authentication required).
- `GET /api/orders/:id` - Get order details by ID (User authentication required).

7. **Testing:**

Use Postman or any other API testing tool to test the endpoints.

---

# **GraphQL Gateway - README**

## **Overview**

The GraphQL Gateway aggregates data from multiple microservices, including the User, Product, and Order services. It provides a unified GraphQL API for client applications to interact with these services.

## **Features**

- Unified GraphQL API.
- Integration with User, Product, and Order microservices.
- JWT-based authentication for protected queries and mutations.

## **Technologies**

- **Node.js** with **Apollo Server**
- **GraphQL** for querying
- **Axios** for HTTP requests
- **JWT** for authentication

## **Setup Instructions**

1. **Clone the repository:**

```bash
git clone <repository-url>
cd graphql-gateway
```

2. **Install dependencies:**

```bash
npm install
```

3. **Set up environment variables:**

Create a `.env` file in the root directory with the following variables:

```
PORT=4000
USER_SERVICE_URL=http://localhost:3001/api/users
PRODUCT_SERVICE_URL=http://localhost:3002/api/products
ORDER_SERVICE_URL=http://localhost:3003/api/orders
JWT_SECRET=your_jwt_secret
```

4. **Run the GraphQL Gateway:**

```bash
npm run dev
```

5. **Access the Playground:**

Open the GraphQL Playground in your browser at `http://localhost:4000/graphql`.

6. **Example Queries and Mutations:**

- **Login Mutation**

```graphql
mutation {
  loginUser(username: "john", password: "password123") {
    token
  }
}
```

- **Place Order Mutation**

```graphql
mutation {
  placeOrder(input: {
    products: [{ productId: "product-id", quantity: 2 }]
  }) {
    orderId
  }
}
```

- **Fetch Users Query**

```graphql
query {
  users {
    id
    username
    profile {
      email
      address
    }
  }
}
```

7. **Testing:**

Use the GraphQL Playground to run queries and mutations or use Postman for testing GraphQL APIs.

---

# **Docker Setup Instructions (Optional)**

To simplify the setup of all services, you can use Docker and Docker Compose to run all services, MongoDB, and RabbitMQ in containers.

## **Docker Compose Setup**

```yaml
version: '3'
services:
  user-service:
    build: ./user-service
    ports:
      - '3005:3005'
    environment:
      - PORT=3001
      - MONGODB_URI=mongodb://mongodb:27017/userdb
      - JWT_SECRET=your_jwt_secret
      - RABBITMQ_URL=amqp://rabbitmq
    depends_on:
      - mongodb
      - rabbitmq

  product-service:
    build: ./product-service
    ports:
      - '3006:3006'
    environment:
      - PORT=3002
      - MONGODB_URI=mongodb://mongodb:27017/productdb
      - JWT_SECRET=your_jwt_secret
      - RABBITMQ_URL=amqp://rabbitmq
    depends_on:
      - mongodb
      - rabbitmq

  order-service:
    build: ./order-service
    ports:
      - '3002:3002'
    environment:
      - PORT=3003
      - MONGODB_URI=mongodb://mongodb:27017/orderdb
      - JWT_SECRET=your_jwt_secret
      - RABBITMQ_URL=amqp://rabbitmq
    depends_on:
      - mongodb
      - rabbitmq

  graphql-gateway:
    build: ./graphql-gateway
    ports:
      - '4000:4000'
    environment:
      - USER_SERVICE_URL=http://user-service:3001/api/users
      - PRODUCT_SERVICE_URL=http://product-service:3002/api/products
      - ORDER_SERVICE_URL=http://order-service:3003/api/orders
      - JWT_SECRET=your_jwt_secret
    depends_on:
      - user-service
      - product-service
      - order-service

  mongodb:
    image: mongo
    ports:
      - '27017:27017'

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - '5672:5672'
      - '15672:15672'
```

## **Run with Docker Compose**

```bash
docker-compose up --build
```
  
This will start all services, including MongoDB and RabbitMQ.

---

###

By following these instructions, you will be able to set up and run the entire microservices architecture and GraphQL gateway.
