# Spring-Boot-Postgres



#### Pom.xml:


```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>


```


#### User.class:

```

@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String address;
    //getters and setters
}

```

#### Create a repository interface UserRepository which extends JpaRepository:


```

public interface UserRepository extends JpaRepository<User, Long> {
    // additional methods go here
}


```


#### Create a service class UserService which uses the repository to perform CRUD:


```

@Service
public class UserService {
    @Autowired
    private UserRepository repository;

    public User addUser(User user) {
        return repository.save(user);
    }

    public List<User> getUsers() {
        return repository.findAll();
    }

    public List<User> getUserbyAddress(String address) {
        return repository.findByAddress(address);
    }

    public void deleteUser(User user) {
        repository.delete(user);
    }
}


```

#### Create a REST controller UserController which maps HTTP requests to the service methods:


```

@RestController
public class UserController {
    @Autowired
    private UserService service;

    @PostMapping(value = "/save")
    public User saveUser(@RequestBody User user) {
        return service.addUser(user);
    }

    @GetMapping("/getUsers")
    public List<User> findAllUsers() {
        return service.getUsers();
    }

    @GetMapping("/getUserByAddress/{address}")
    public List<User> findUserByAddress(@PathVariable String address) {
        return service.getUserbyAddress(address);
    }

    @DeleteMapping(value="/remove")
    public User removeUser(@RequestBody User user) {
        service.deleteUser(user);
        return user;
    }
}


```



#### Application.properties:

```

spring.datasource.url=jdbc:postgresql://host:port/dbname
spring.datasource.username=username
spring.datasource.password=password
spring.jpa.generate-ddl=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.PostgreSQLDialect


```


#### Junittese case:

```

import static org.assertj.core.api.Assertions.assertThat;

import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import com.javatechie.spring.postgres.api.model.Employee;
import com.javatechie.spring.postgres.api.repository.EmployeeRepository;
import com.javatechie.spring.postgres.api.service.EmployeeService;

@RunWith(SpringRunner.class)
@SpringBootTest
public class EmployeeServiceTest {

    @Autowired
    private EmployeeService service;
    
    @Autowired
    private EmployeeRepository repository;

    @Test
    public void testSaveEmployee() {
        Employee employee = new Employee(0, "John Doe", "Software Engineer");
        Employee saved = service.saveEmployee(employee);
        assertThat(saved.getId()).isGreaterThan(0);
        assertThat(saved.getName()).isEqualTo("John Doe");
        assertThat(saved.getDesignation()).isEqualTo("Software Engineer");
    }

    @Test
    public void testGetEmployees() {
        List<Employee> employees = service.getEmployees();
        assertThat(employees).isNotEmpty();
        assertThat(employees.size()).isEqualTo(repository.findAll().size());
    }

    @Test
    public void testGetEmployeeById() {
        Employee employee = service.getEmployeeById(1);
        assertThat(employee.getId()).isEqualTo(1);
        assertThat(employee.getName()).isEqualTo("Jane Doe");
        assertThat(employee.getDesignation()).isEqualTo("Software Developer");
    }

    @Test
    public void testDeleteEmployee() {
        service.deleteEmployee(1);
        assertThat(repository.findById(1).isPresent()).isFalse();
    }
}


```


Preparing for a PostgreSQL interview involves understanding both basic and advanced concepts, as well as practical skills. Here are some common PostgreSQL interview questions along with brief explanations to help you prepare:

### Basic Questions

1. **What is PostgreSQL?**
   - **Answer:** PostgreSQL is an open-source, object-relational database management system (ORDBMS) known for its robustness, extensibility, and compliance with SQL standards.

2. **What are some key features of PostgreSQL?**
   - **Answer:** ACID compliance, support for advanced data types (JSON, arrays, hstore), full-text search, indexing, extensibility (custom functions, data types), MVCC (Multi-Version Concurrency Control), and strong support for procedural languages (PL/pgSQL, PL/Python).

3. **What is MVCC in PostgreSQL?**
   - **Answer:** Multi-Version Concurrency Control (MVCC) is a method used to handle concurrent transactions without locking the database. It allows readers to see a consistent snapshot of the database, ensuring isolation and improving performance.

4. **What are tablespaces in PostgreSQL?**
   - **Answer:** Tablespaces are storage locations on disk where PostgreSQL stores database objects. They allow administrators to control the physical location of data and can be used to optimize performance and manage disk space.

5. **How do you create a database in PostgreSQL?**
   - **Answer:**
     ```sql
     CREATE DATABASE database_name;
     ```

6. **What is the difference between `CHAR`, `VARCHAR`, and `TEXT` data types in PostgreSQL?**
   - **Answer:** `CHAR(n)` is a fixed-length character type, `VARCHAR(n)` is a variable-length character type with a limit, and `TEXT` is a variable-length character type without a specific length limit.

### Intermediate Questions

7. **How do you back up a PostgreSQL database?**
   - **Answer:** Use the `pg_dump` utility for logical backups or `pg_basebackup` for physical backups. Example:
     ```sh
     pg_dump -U username -F c database_name > backup_file.dump
     ```

8. **How do you restore a PostgreSQL database from a backup?**
   - **Answer:** Use the `pg_restore` utility for restoring from a custom-format dump. Example:
     ```sh
     pg_restore -U username -d database_name -1 backup_file.dump
     ```

9. **What are the different types of indexes in PostgreSQL?**
   - **Answer:** B-tree, Hash, GiST, SP-GiST, GIN, and BRIN indexes. Each type is optimized for different types of queries and data structures.

10. **How do you create and use a composite primary key in PostgreSQL?**
    - **Answer:**
      ```sql
      CREATE TABLE example (
          column1 datatype,
          column2 datatype,
          PRIMARY KEY (column1, column2)
      );
      ```

11. **What is a CTE (Common Table Expression)?**
    - **Answer:** A CTE is a temporary result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. It is defined using the `WITH` keyword.

12. **How do you handle JSON data in PostgreSQL?**
    - **Answer:** PostgreSQL supports JSON and JSONB data types for storing JSON data. You can use functions and operators like `->`, `->>`, `#>>`, and functions such as `jsonb_set`, `jsonb_insert`, `jsonb_build_object` to query and manipulate JSON data.

### Advanced Questions

13. **What are the different types of joins in PostgreSQL?**
    - **Answer:** INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN, CROSS JOIN, and SELF JOIN.

14. **Explain the concept of foreign data wrappers (FDW) in PostgreSQL.**
    - **Answer:** FDWs allow PostgreSQL to query data from other databases and data sources as if they were part of the local database. You can use `CREATE EXTENSION` to enable and `CREATE SERVER` and `CREATE FOREIGN TABLE` to configure.

15. **How does PostgreSQL handle replication?**
    - **Answer:** PostgreSQL supports streaming replication, logical replication, and synchronous replication. Streaming replication is typically used for high availability, while logical replication is used for data distribution and synchronization.

16. **What is a materialized view and how do you refresh it?**
    - **Answer:** A materialized view stores the result of a query physically. You refresh it to update the data using:
      ```sql
      REFRESH MATERIALIZED VIEW view_name;
      ```

17. **How do you optimize query performance in PostgreSQL?**
    - **Answer:** Use indexes, analyze query plans using `EXPLAIN`, tune database configuration parameters, use partitioning, optimize schema design, and use appropriate data types.

18. **What is the role of `pg_stat_statements` in PostgreSQL?**
    - **Answer:** `pg_stat_statements` is an extension that tracks execution statistics of all SQL statements executed by a server. It helps in monitoring query performance and identifying slow queries.

19. **Explain the `VACUUM` command in PostgreSQL.**
    - **Answer:** The `VACUUM` command is used to reclaim storage occupied by dead tuples, which are outdated or deleted rows. `VACUUM FULL` rebuilds the entire table to reclaim the space, while a plain `VACUUM` only cleans up.

20. **How do you set up connection pooling in PostgreSQL?**
    - **Answer:** Use a connection pooler like PgBouncer or Pgpool-II to manage database connections efficiently by reusing and limiting the number of connections.

### Practical Tasks

- Write a query to find the nth highest salary from an employee table.
- Explain how to set up and configure a PostgreSQL cluster for high availability.
- Demonstrate how to use window functions in PostgreSQL.
- Show how to use triggers and stored procedures.

Preparing for these questions and understanding the underlying concepts will help you succeed in a PostgreSQL interview.

