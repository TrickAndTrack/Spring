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

> **_NOTE:_**  spring -> IOC for that we need to create manually. 
Advance of spring -> Spring MVC-> IOC was converted in one line means in the form of annotation.


# Spring - IoC Containers
In the Spring framework, the IOC container is responsible to inject the dependency. We provide metadata to the IOC container either by XML file or annotation. (Only we need to show BEAN and CONFIG. FILE to the container)
The Spring IoC container makes use of Java POJO classes and configuration metadata to produce a fully configured and executable system or application.

## example of using an IoC container in Spring :

we have a class Person that we want to create and configure using Spring's IoC container:
```java
public class Person {
   private String name;
   private int age;
   
   public Person(String name, int age) {
       this.name = name;
       this.age = age;
   }
   
   public void sayHello() {
       System.out.println("Hello, my name is " + name + " and I am " + age + " years old.");
   }
}

```

We can create an instance of Person and set its properties using the following Spring configuration file:
```java

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   
   <bean id="person" class="com.example.Person">
      <constructor-arg value="John Doe" />
      <constructor-arg value="30" />
   </bean>
   
</beans>

```

This configuration file defines a bean with the id person that is an instance of the Person class, with its name property set to "John Doe" and its age property set to 30.

To load and use this bean in our Java code, we can create a ApplicationContext object and use it to get the Person bean:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
      Person person = (Person) context.getBean("person");
      person.sayHello();
   }
}
```

This code loads the Spring configuration file using a ClassPathXmlApplicationContext, gets the person bean from the application context, and calls its sayHello() method. The output should be:

```java
Hello, my name is John Doe and I am 30 years old.

```


> **_Note:_** The ApplicationContext includes all functionality of the BeanFactory, It is generally recommended over BeanFactory


# Spring - Dependency Injection

One of its key features in Spring framework is dependency injection, which is a design pattern that allows objects to be created without explicitly specifying all of their dependencies.
it is a design pattern that removes the dependency between the object. dependency injection creates the loosely coupled object.

> **_Note:_** (IoC) is a general concept, Dependency Injection is merely one concrete example of Inversion of Control.

## Here's an example of how dependency injection works in Spring:

Suppose we have a class called UserService that needs an instance of UserRepository to perform database operations. Without dependency injection, we would have to create an instance of UserRepository inside UserService:

```java
public class UserService {
    private UserRepository userRepository;

    public UserService() {
        userRepository = new UserRepository();
    }
}
```
With dependency injection, we can instead define the dependency in a configuration file or annotation:
```java
@Configuration
public class AppConfig {
    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }

    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }
}
```
In this example, we've defined a UserRepository bean and a UserService bean, which has a constructor that takes a UserRepository parameter. The userService() method uses the userRepository() method to create a UserRepository bean and pass it to the UserService constructor.

When we run our application, the Spring container will create both beans and inject the UserRepository instance into the UserService. This allows us to write more modular and testable code, since we can easily swap out dependencies or mock them for testing purposes.


## Another Example how to implement Dependency Injection using Spring in Java:

Let's assume we have a Student class with a StudentService class that depends on it. To use dependency injection with Spring, we can define a bean for StudentService that has a reference to the Student bean, like this:

```java
// Student.java
public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getters and setters
}

// StudentService.java
public class StudentService {
    private Student student;

    public StudentService(Student student) {
        this.student = student;
    }

    public void printStudentDetails() {
        System.out.println("Name: " + student.getName());
        System.out.println("Age: " + student.getAge());
    }
}

// AppConfig.java
@Configuration
public class AppConfig {
    @Bean
    public Student student() {
        return new Student("John Doe", 20);
    }

    @Bean
    public StudentService studentService() {
        return new StudentService(student());
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        StudentService studentService = context.getBean(StudentService.class);
        studentService.printStudentDetails();
    }
}
```
In this example, we define two beans in the AppConfig class: Student and StudentService. The StudentService bean has a constructor that takes a Student parameter, which is passed to it by Spring when it creates the bean.

In the Main class, we create an instance of ApplicationContext using the AnnotationConfigApplicationContext class, passing in the AppConfig class as a parameter. We then use the getBean method of the ApplicationContext to retrieve an instance of the StudentService bean, which we can then use to call the printStudentDetails method.

When we run the Main class, the output will be:
```java
Name: John Doe
Age: 20
```

> **_NOTE:_**  we have disadvantage in core java that is we need to create object using new keyword. for that reasone we create loose couple object.
> employee emp: Example of a loose couple of objects	
> tight couple object: we use new keyword
> dependency part translates into an association between two classes.For example, class A is dependent on class B.
> Now, let's look at the second part, injection. All this means is, that class B will get injected into class A by the IoC.


# XML VS ANNOTATION

#### annotauion
```java\
@Configuration
public class AppConfig {
    @Bean
    public Engine engine() {
        return new Engine();
    }

    @Bean
    public Transmission transmission() {
        return new Transmission();
    }

    @Bean
    public Car car() {
        return new Car(engine(), transmission());
    }
}
```
##### xml
```java
<!-- applicationContext.xml -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="engine" class="com.example.Engine" />
    <bean id="transmission" class="com.example.Transmission" />
    <bean id="car" class="com.example.Car">
        <constructor-arg ref="engine" />
        <constructor-arg ref="transmission" />
    </bean>

</beans>

```

#  Spring Setter-based Dependency Injection
Setter-based Dependency Injection is a type of Dependency Injection design pattern in which the dependencies of a class are injected using setter methods instead of constructor arguments. This pattern is commonly used in object-oriented programming, especially in the context of the Spring Framework in Java.

In Setter-based Dependency Injection, the class that requires dependencies provides a public method for each dependency that it requires. These methods, typically named with the prefix "set", are called "setter methods". The dependency injection framework calls these setter methods to provide the required dependencies to the class.

## how to achieve Setter-based Dependency Injection in xml and Annoation.
# Example in xml

Create the dependency class:
```java
public class UserRepository {
    // implementation
}
```
Create the dependent class with the setter method for the dependency injection:

```java
public class UserService {
    private UserRepository userRepository;

    // Setter method for dependency injection
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // Methods that use userRepository
}
```

Create the XML configuration file (e.g., applicationContext.xml) that specifies the beans to be injected and their dependencies:

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
                        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Bean definition for the dependent class -->
    <bean id="userService" class="com.example.UserService">
        <property name="userRepository" ref="userRepository" />
    </bean>

    <!-- Bean definition for the dependency class -->
    <bean id="userRepository" class="com.example.UserRepository" />
</beans>
```

In the above XML configuration file, we define two beans - userService and userRepository. We set the userRepository bean as a dependency for the userService bean using the <property> tag.


Use the dependent object in the application:

```java
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) context.getBean("userService");
        // use userService object
    }
}
```
In the above code snippet, we retrieve the userService object from the Spring application context and use it as required.



That's it! This is how you can use Setter-based Dependency Injection in Java using XML format.

# Example in Annotation
Create the dependency class:


