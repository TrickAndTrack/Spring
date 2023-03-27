# Spring
Spring and Spring Boot Notes

## IOC: Inversion of control
it is responsible to create a loosely coupled object 
#### how to create loosely coupled object spring internally

Assume we have a UserService class that depends on a UserRepository interface to perform database operations.
1. Define the UserService class with a private field for UserRepository.
```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // ... other methods that use userRepository
}

```
2. Define the UserRepository interface.
```java
public interface UserRepository {
    List<User> findAll();
    User findById(long id);
    void save(User user);
    void deleteById(long id);
}

```
3. Implement the UserRepository interface.

```java
@Repository
public class UserRepositoryImpl implements UserRepository {
    // implementation of UserRepository methods
}

```
4. Configure the Spring container to create and manage the objects.
```java
@Configuration
public class AppConfig {
    @Bean
    public UserRepository userRepository() {
        return new UserRepositoryImpl();
    }

    @Bean
    public UserService userService(UserRepository userRepository) {
        return new UserService(userRepository);
    }
}

```
In this example, the UserService is loosely coupled with the UserRepository interface because it only depends on the interface, not a specific implementation.
The implementation is injected at runtime by Spring using the @Autowired annotation on the constructor of UserService. 
This allows for flexibility in swapping out different implementations of UserRepository without affecting the UserService.
