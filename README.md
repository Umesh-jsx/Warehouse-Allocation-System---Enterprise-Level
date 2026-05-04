Warehouse Allocation System
Enterprise-level Spring Boot application for managing product distribution across multiple warehouses.

Steps — Run the Application
Expand project → src/main/java → com.warehouse
Right-click WarehouseAllocationApplication.java
Select Run As → Java Application
Wait for: Started WarehouseAllocationApplication in X seconds
Step for — Verify Running
Open browser:

Swagger UI: http://localhost:8080/swagger-ui.html
H2 Console: http://localhost:8080/h2-console
JDBC URL: jdbc:h2:mem:warehousedb
Username: sa | Password:(empty)
API Endpoints
Warehouses
| Method | URL | Description | |--------|-----|-------------| | POST | /api/v1/warehouses | Create warehouse | | GET | /api/v1/warehouses | List all warehouses | | GET | /api/v1/warehouses/{id} | Get warehouse | | PUT | /api/v1/warehouses/{id} | Update warehouse | | PATCH | /api/v1/warehouses/{id}/activate | Activate | | PATCH | /api/v1/warehouses/{id}/deactivate | Deactivate | | DELETE | /api/v1/warehouses/{id} | Soft delete |

Products
| Method | URL | Description | |--------|-----|-------------| | POST | /api/v1/products | Create product | | GET | /api/v1/products | List all products | | GET | /api/v1/products/{id} | Get product | | PUT | /api/v1/products/{id} | Update product | | DELETE | /api/v1/products/{id} | Delete product |

Inventory
| Method | URL | Description | |--------|-----|-------------| | POST | /api/v1/inventory/assign | Assign stock to warehouse | | GET | /api/v1/inventory/warehouse/{id} | Get warehouse inventory | | GET | /api/v1/inventory/product/{id} | Get product distribution |

Allocations
| Method | URL | Description | |--------|-----|-------------| | POST | /api/v1/allocations | Allocate product | | GET | /api/v1/allocations/{id} | Get allocation | | GET | /api/v1/allocations/search | Search (paginated) |

Stock Transfers
| Method | URL | Description | |--------|-----|-------------| | POST | /api/v1/stock-transfers | Transfer stock | | GET | /api/v1/stock-transfers | List transfers | | GET | /api/v1/stock-transfers/product/{id} | By product |

Quick Test (Sample Requests)
1. Create a Warehouse
POST /api/v1/warehouses
{
  "name": "East Hub",
  "location": "Boston",
  "capacity": 500
}
2. Create a Product
POST /api/v1/products
{
  "name": "Gaming Keyboard",
  "sku": "KEY-001",
  "totalStock": 200
}
3. Assign Inventory
POST /api/v1/inventory/assign?warehouseId=1&productId=1&quantity=100
4. Allocate (auto-selects best warehouse)
POST /api/v1/allocations
{
  "productId": 1,
  "quantity": 20
}
5. Search Allocations
GET /api/v1/allocations/search?productId=1&page=0&size=10&sortBy=allocatedAt
Run Unit Tests
In Eclipse:
Right-click project → Run As → JUnit Test
All tests in src/test/java will run
In Terminal:
mvn test
mvn test jacoco:report   # generates coverage report
Coverage report: target/site/jacoco/index.html

MySQL Production Setup (Optional)
Update application.properties: `properties spring.datasource.url=jdbc:mysql://localhost:3306/warehousedb spring.datasource.username=root spring.datasource.password=yourpassword spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect spring.jpa.hibernate.ddl-auto=update spring.h2.console.enabled=false `

Design Decisions
Optimistic Locking — @Version on WarehouseInventory prevents lost updates under concurrency
Pessimistic Lock — PESSIMISTIC_WRITE lock used during allocation to prevent race conditions
Soft Delete — Warehouses use deleted flag; products use hard delete
Auto Warehouse Selection — System picks warehouse with most available stock for the product
Layered Architecture — Controller → Service → Repository (no business logic in controllers)
H2 Default — In-memory DB for easy local dev/testing; MySQL-ready for production
Pagination — All list endpoints that could grow large support page, size, sortBy
