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

