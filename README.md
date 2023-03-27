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

## 2 Types of IOC container 
### BeanFactory :
-it is an interface define in org.springframework.beans.factory.BeanFactory
-support for dependency injection.
-it is based on factory design pattern.
-support to create a standalone application.
### Example 
```java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.core.io.ClassPathResource;

public class BeanFactoryExample {
    public static void main(String[] args) {
        // Load the bean definitions from an XML file
        BeanFactory factory = new XmlBeanFactory(new ClassPathResource("beans.xml"));

        // Get an instance of a bean
        MyBean bean = (MyBean) factory.getBean("myBean");
        bean.doSomething();
    }
}

```
In this example, we use the XmlBeanFactory to create an IoC container from an XML file containing the bean definitions. We then retrieve an instance of the MyBean class from the container using the getBean() method.

### ApplicationContext :
it is an interface defined in org. spring framework.context.ApplicationContext
-Application Context extends Bean factory.
-it is based on a factory design pattern.
-support to create standalone applications and web application.

### Example
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ApplicationContextExample {
    public static void main(String[] args) {
        // Load the bean definitions from an XML file
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        // Get an instance of a bean
        MyBean bean = (MyBean) context.getBean("myBean");
        bean.doSomething();
    }
}

```
In this example, we use the ClassPathXmlApplicationContext to create an IoC container from an XML file containing the bean definitions. We then retrieve an instance of the MyBean class from the container using the getBean() method.

Both examples demonstrate the basic usage of IoC containers in Spring. The main difference between the two is that the BeanFactory provides lazy initialization of beans, while the ApplicationContext provides eager initialization of beans by default.
