# [Enzo Barbosa] - Java/Spring Boot Developer 💻

## 🛠 Core Expertise
- **Java 8+** (Streams, Lambdas, Collections)
- **Spring Boot** (REST APIs, Spring Security, JPA)
- **SQL Databases** (PostgreSQL, MySQL, Query Optimization)
- **Testing** (JUnit 5, Mockito, TestContainers)
- **DevOps** (Docker, Kubernetes, CI/CD Pipelines)

## 🚀 Spring Boot Code Samples

### REST Controller with Validation
```java
@RestController
@RequestMapping("/api/users")
@Validated
public class UserController {

    private final UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getUserById(@PathVariable @Min(1) Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }

    @PostMapping
    public ResponseEntity<UserDTO> createUser(@Valid @RequestBody UserCreationDTO userDTO) {
        UserDTO createdUser = userService.createUser(userDTO);
        URI location = ServletUriComponentsBuilder.fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(createdUser.getId())
                .toUri();
        return ResponseEntity.created(location).body(createdUser);
    }
}
```

### Spring Data JPA Repository
```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
           SELECT o FROM Order o 
           JOIN FETCH o.customer c 
           WHERE o.status = :status 
           AND o.createdAt >= :since
           """)
    List<Order> findOrdersByStatusSince(
        @Param("status") OrderStatus status,
        @Param("since") LocalDateTime since);

    @Modifying
    @Query("UPDATE Order o SET o.status = :status WHERE o.id = :id")
    int updateOrderStatus(@Param("id") Long id, @Param("status") OrderStatus status);
}
```

## 📊 SQL Expertise

### Complex Query Example
```sql
WITH customer_stats AS (
    SELECT 
        c.id,
        c.name,
        COUNT(o.id) AS total_orders,
        SUM(o.total_amount) AS lifetime_value,
        MAX(o.created_at) AS last_order_date
    FROM customers c
    LEFT JOIN orders o ON c.id = o.customer_id
    WHERE o.status = 'COMPLETED'
    GROUP BY c.id, c.name
)
SELECT 
    cs.*,
    DENSE_RANK() OVER (ORDER BY lifetime_value DESC) AS value_rank
FROM customer_stats cs
WHERE total_orders > 5
ORDER BY lifetime_value DESC
LIMIT 20;
```

### Database Schema Design
```sql
CREATE TABLE products (
    id BIGSERIAL PRIMARY KEY,
    sku VARCHAR(32) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) CHECK (price > 0),
    stock_quantity INTEGER DEFAULT 0 CHECK (stock_quantity >= 0),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE product_categories (
    product_id BIGINT REFERENCES products(id) ON DELETE CASCADE,
    category_id BIGINT REFERENCES categories(id) ON DELETE CASCADE,
    PRIMARY KEY (product_id, category_id)
);
```

## 🧪 Testing Examples

### Integration Test with TestContainers
```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class ProductRepositoryIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15-alpine");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private ProductRepository productRepository;

    @Test
    void shouldFindProductsByPriceRange() {
        Product cheap = productRepository.save(new Product("SKU1", "Cheap", 9.99));
        Product expensive = productRepository.save(new Product("SKU2", "Expensive", 99.99));
        
        List<Product> results = productRepository.findByPriceBetween(10.00, 100.00);
        
        assertThat(results).containsExactly(expensive);
    }
}
```

## 🔗 Connect With Me
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](your-linkedin)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:your-email)
