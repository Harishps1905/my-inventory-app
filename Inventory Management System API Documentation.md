<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Inventory Management System API Documentation

## API Overview

This REST API provides core functionality for managing an e-commerce inventory system with minimal features suitable for a POC. The system tracks products, inventory levels, purchase orders, and provides basic reporting capabilities.

**Base URL**: `https://your-app.vercel.app/api`

**Tech Stack**: Next.js API routes with Supabase PostgreSQL backend

## Authentication

All API endpoints require authentication using Supabase Auth.

```javascript
Headers: {
  "Authorization": "Bearer <access_token>",
  "Content-Type": "application/json"
}
```


## Core API Endpoints

### 1. Product Catalog Management

#### Get All Products

```
GET /api/products
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Product Name",
      "sku": "PROD-001",
      "category": "Electronics",
      "price": 299.99,
      "description": "Product description",
      "specifications": {
        "weight": "1.2kg",
        "dimensions": "10x10x5cm"
      },
      "created_at": "2025-08-19T18:14:00Z"
    }
  ]
}
```


#### Create Product

```
POST /api/products
```

**Request Body:**

```json
{
  "name": "New Product",
  "sku": "PROD-002",
  "category": "Electronics",
  "price": 199.99,
  "description": "Product description",
  "specifications": {
    "weight": "0.8kg",
    "color": "Black"
  }
}
```


#### Update Product

```
PUT /api/products/[id]
```


#### Delete Product

```
DELETE /api/products/[id]
```


### 2. Inventory Tracking

#### Get Inventory Levels

```
GET /api/inventory
```

**Query Parameters:**

- `warehouse_id` (optional): Filter by specific warehouse
- `product_id` (optional): Filter by specific product

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "product_id": "uuid",
      "warehouse_id": "uuid",
      "quantity": 150,
      "reserved_quantity": 25,
      "available_quantity": 125,
      "reorder_level": 50,
      "last_updated": "2025-08-19T18:14:00Z",
      "product": {
        "name": "Product Name",
        "sku": "PROD-001"
      },
      "warehouse": {
        "name": "Main Warehouse",
        "location": "New York"
      }
    }
  ]
}
```


#### Update Inventory

```
PUT /api/inventory/[id]
```

**Request Body:**

```json
{
  "quantity": 200,
  "reorder_level": 60
}
```


#### Inventory Movement (Stock In/Out)

```
POST /api/inventory/movement
```

**Request Body:**

```json
{
  "product_id": "uuid",
  "warehouse_id": "uuid",
  "type": "IN", // "IN" or "OUT"
  "quantity": 50,
  "reference": "PO-001",
  "notes": "Received from supplier"
}
```


### 3. Warehouse Management

#### Get All Warehouses

```
GET /api/warehouses
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Main Warehouse",
      "location": "New York, NY",
      "address": "123 Storage St",
      "is_active": true,
      "created_at": "2025-08-19T18:14:00Z"
    }
  ]
}
```


#### Create Warehouse

```
POST /api/warehouses
```


### 4. Purchase Orders

#### Get Purchase Orders

```
GET /api/purchase-orders
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "po_number": "PO-001",
      "supplier_name": "ABC Supplier",
      "status": "PENDING", // PENDING, ORDERED, RECEIVED, CANCELLED
      "total_amount": 2999.50,
      "order_date": "2025-08-19T18:14:00Z",
      "expected_date": "2025-08-25T00:00:00Z",
      "items": [
        {
          "product_id": "uuid",
          "quantity": 10,
          "unit_price": 299.95,
          "total_price": 2999.50,
          "product": {
            "name": "Product Name",
            "sku": "PROD-001"
          }
        }
      ]
    }
  ]
}
```


#### Create Purchase Order

```
POST /api/purchase-orders
```

**Request Body:**

```json
{
  "supplier_name": "XYZ Supplier",
  "expected_date": "2025-08-30",
  "items": [
    {
      "product_id": "uuid",
      "quantity": 20,
      "unit_price": 199.99
    }
  ]
}
```


#### Update Purchase Order Status

```
PATCH /api/purchase-orders/[id]/status
```

**Request Body:**

```json
{
  "status": "RECEIVED"
}
```


### 5. Stock Alerts

#### Get Active Alerts

```
GET /api/alerts
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "type": "LOW_STOCK", // LOW_STOCK, OUT_OF_STOCK
      "product_id": "uuid",
      "warehouse_id": "uuid",
      "current_quantity": 15,
      "reorder_level": 50,
      "severity": "HIGH", // LOW, MEDIUM, HIGH
      "created_at": "2025-08-19T18:14:00Z",
      "is_acknowledged": false,
      "product": {
        "name": "Product Name",
        "sku": "PROD-001"
      },
      "warehouse": {
        "name": "Main Warehouse"
      }
    }
  ]
}
```


#### Acknowledge Alert

```
PATCH /api/alerts/[id]/acknowledge
```


### 6. Reporting Dashboard

#### Inventory Summary

```
GET /api/reports/inventory-summary
```

**Response:**

```json
{
  "success": true,
  "data": {
    "total_products": 150,
    "total_stock_value": 125750.50,
    "low_stock_items": 12,
    "out_of_stock_items": 3,
    "warehouses": 2,
    "categories": [
      {
        "name": "Electronics",
        "product_count": 75,
        "stock_value": 87500.25
      }
    ]
  }
}
```


#### Stock Turnover Report

```
GET /api/reports/turnover
```

**Query Parameters:**

- `period`: "7d", "30d", "90d" (default: "30d")

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "product_id": "uuid",
      "product_name": "Product Name",
      "sku": "PROD-001",
      "beginning_inventory": 200,
      "ending_inventory": 150,
      "units_sold": 100,
      "turnover_ratio": 0.57,
      "average_inventory": 175
    }
  ]
}
```


#### Low Stock Report

```
GET /api/reports/low-stock
```


## Database Schema (Supabase Tables)

### Products Table

```sql
CREATE TABLE products (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name VARCHAR NOT NULL,
  sku VARCHAR UNIQUE NOT NULL,
  category VARCHAR NOT NULL,
  price DECIMAL(10,2),
  description TEXT,
  specifications JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```


### Warehouses Table

```sql
CREATE TABLE warehouses (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name VARCHAR NOT NULL,
  location VARCHAR,
  address TEXT,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW()
);
```


### Inventory Table

```sql
CREATE TABLE inventory (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  product_id UUID REFERENCES products(id),
  warehouse_id UUID REFERENCES warehouses(id),
  quantity INTEGER DEFAULT 0,
  reserved_quantity INTEGER DEFAULT 0,
  reorder_level INTEGER DEFAULT 10,
  last_updated TIMESTAMP DEFAULT NOW(),
  UNIQUE(product_id, warehouse_id)
);
```


### Purchase Orders Table

```sql
CREATE TABLE purchase_orders (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  po_number VARCHAR UNIQUE NOT NULL,
  supplier_name VARCHAR NOT NULL,
  status VARCHAR DEFAULT 'PENDING',
  total_amount DECIMAL(10,2),
  order_date TIMESTAMP DEFAULT NOW(),
  expected_date TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW()
);
```


## Error Handling

All endpoints return consistent error responses:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Product SKU already exists",
    "details": {}
  }
}
```

**Common Error Codes:**

- `VALIDATION_ERROR`: Invalid input data
- `NOT_FOUND`: Resource not found
- `UNAUTHORIZED`: Authentication required
- `FORBIDDEN`: Insufficient permissions
- `INTERNAL_ERROR`: Server error


## Getting Started

1. **Setup Supabase Project**: Create tables using the provided schema
2. **Environment Variables**: Configure Next.js with Supabase credentials
3. **API Routes**: Implement endpoints in `/pages/api/` or `/app/api/`
4. **Authentication**: Setup Supabase Auth for API protection

This minimal API provides the foundation for a functional inventory management system suitable for POC development and can be extended with additional features as needed.

