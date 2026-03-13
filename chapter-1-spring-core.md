# Chapter 1 — Spring Core

[← Back to Index](../README.md)

---

## 1. What is Spring Framework?

**Spring** is a Java framework used to build enterprise (large-scale) applications.

It helps developers write applications in a **simple, modular, and clean** way — instead of manually managing everything yourself.

### Spring provides features like:

| Feature | What it does |
|---------|-------------|
| **Dependency Injection** | Automatically provides objects a class needs |
| **Inversion of Control** | Spring manages object creation, not you |
| **Transaction Management** | Handles database transactions cleanly |
| **Web Development** | Build web apps with Spring MVC |
| **Security** | Protect your app with Spring Security |

> **Analogy:** Think of Spring as a helper who sets up and manages everything behind the scenes, so you can focus on writing business logic.

---

## 2. What is Spring Core?

**Spring Core** is the **base/foundation module** of the entire Spring Framework.

Everything in Spring is built on top of Spring Core. It provides the main tools for managing objects in your application.

### Important concepts in Spring Core:

- IoC (Inversion of Control)
- Dependency Injection
- Beans
- Application Context
- Bean Lifecycle

---

## 3. What is a Bean?

A **Bean** is simply a **Java object that is managed by Spring**.

Normally you create objects yourself using `new`. In Spring, you let the framework create and manage them for you.

```java
// Normal Java — you create the object
Student s = new Student();

// Spring — Spring creates and manages it for you
// You just ask for it when needed
```

```java
// Example Bean class
public class Student {
    private String name;

    public Student() {
        System.out.println("Student object created");
    }
}
```

> **Key idea:** You define the class, Spring creates the object and handles its entire life — creation, wiring, and destruction.

---

## 4. What is IoC (Inversion of Control)?

**Inversion of Control** means the **control of creating objects shifts from you (the developer) to Spring**.

### Without IoC (normal Java):
```java
// You are in control — you create everything manually
Student s = new Student();
Engine e = new Engine();
Car car = new Car(e);
```

### With IoC (Spring):
```java
// Spring is in control — you just ask for what you need
Car car = context.getBean("car", Car.class);
// Spring already created Car and Engine and connected them
```

> **Simple analogy:** Instead of cooking your own food, you go to a restaurant and say "give me a burger." The kitchen (Spring) handles all the preparation — you just receive the result.

---

## 5. Dependency Injection

**Dependency Injection (DI)** means Spring **automatically provides the objects a class needs** (its dependencies).

### The Problem Without DI:
```java
class Car {
    Engine engine = new Engine();  // Car creates its own Engine — tightly coupled
}
```

### The Solution With DI:
```java
class Car {
    Engine engine;  // Car doesn't create Engine — Spring provides it
}
```

- `Car` **depends on** `Engine`.
- Spring **injects** (provides) the `Engine` object into `Car` automatically.
- This makes code **loosely coupled** — easy to change, test, and maintain.

---

## 6. Types of Dependency Injection

### Constructor Injection
The dependency is provided through the **constructor** when the object is created.

```java
class Car {
    Engine engine;

    public Car(Engine engine) {      // Engine is injected via constructor
        this.engine = engine;
    }
}
```

✅ Best when the dependency is **required** (mandatory).

---

### Setter Injection
The dependency is provided through a **setter method** after the object is created.

```java
class Car {
    Engine engine;

    public void setEngine(Engine engine) {   // Engine is injected via setter
        this.engine = engine;
    }
}
```

✅ Best when the dependency is **optional** (can be changed later).

---

| Type | When to use |
|------|------------|
| **Constructor Injection** | Mandatory dependencies |
| **Setter Injection** | Optional dependencies |

---

## 7. Spring Container

The **Spring Container** is the core of Spring — it's the engine that does all the work.

### What does the container do?

- Creates bean objects
- Injects dependencies into them
- Manages their entire lifecycle
- Destroys them when no longer needed

### Two types of containers:

| Container | Description |
|-----------|-------------|
| **BeanFactory** | Basic, lightweight container — rarely used directly |
| **ApplicationContext** | Advanced container — used in almost all applications |

> **Analogy:** The Spring Container is like a **factory** that produces and manages all the objects your application needs.

---

## 8. ApplicationContext

`ApplicationContext` is the **main interface to interact with the Spring container**.

```java
// Loading beans from an XML config file
ApplicationContext context =
    new ClassPathXmlApplicationContext("config.xml");

// Getting a bean from the container
Student s = context.getBean("student", Student.class);
```

- You tell it where the configuration is (XML file, annotation, or Java class).
- It creates all the beans and wires them together.
- You can then ask it for any bean by name or type.

---

## 9. Bean Configuration

Beans can be configured (defined) in **3 ways**:

### Way 1 — XML Configuration (old style)

```xml
<!-- config.xml -->
<bean id="student" class="com.example.Student"/>
```

### Way 2 — Annotation Configuration (modern, most common)

```java
@Component                      // tells Spring: "this is a bean"
public class Student {
}
```

Spring automatically detects classes marked with `@Component` and creates beans for them (called **component scanning**).

### Way 3 — Java Configuration (clean, no XML)

```java
@Configuration                  // tells Spring: "this is a config class"
public class AppConfig {

    @Bean                       // tells Spring: "this method creates a bean"
    public Student student() {
        return new Student();
    }
}
```

---

## 10. Important Annotations

| Annotation | What it does |
|------------|-------------|
| `@Component` | Marks a class as a Spring-managed bean |
| `@Autowired` | Automatically injects the required dependency |
| `@Configuration` | Marks a class as a Spring configuration class |
| `@Bean` | Declares a method that returns a bean object |
| `@Service` | Like `@Component` but for service/business logic classes |
| `@Repository` | Like `@Component` but for database/data access classes |
| `@Controller` | Like `@Component` but for web controllers |

```java
@Component
public class Engine { }

@Component
public class Car {
    @Autowired                  // Spring automatically injects Engine here
    private Engine engine;
}
```

---

## 11. Bean Scope

**Scope** controls how many instances of a bean Spring creates.

| Scope | Behaviour | When to use |
|-------|-----------|------------|
| **Singleton** | Only **one** object created for the whole application | Default — for stateless beans |
| **Prototype** | A **new** object created every time you ask for it | When each user/request needs its own copy |

```java
@Component
@Scope("singleton")     // one shared instance (this is the default)
public class DatabaseConfig { }

@Component
@Scope("prototype")     // new instance every time
public class ShoppingCart { }
```

---

## 12. Bean Lifecycle

Every Spring bean goes through a defined lifecycle:

```
1. Bean is created (instantiated)
        ↓
2. Dependencies are injected
        ↓
3. @PostConstruct method runs (initialization)
        ↓
4. Bean is used by the application
        ↓
5. @PreDestroy method runs (cleanup)
        ↓
6. Bean is destroyed
```

```java
@Component
public class DatabaseConnection {

    @PostConstruct                          // runs right after bean is created
    public void init() {
        System.out.println("Connecting to database...");
    }

    @PreDestroy                             // runs just before bean is destroyed
    public void cleanup() {
        System.out.println("Closing database connection...");
    }
}
```

---

## 13. Advantages of Spring Core

| Advantage | What it means |
|-----------|--------------|
| **Reduces complexity** | Spring handles object creation — less boilerplate code |
| **Loosely coupled** | Classes don't depend directly on each other — easy to swap parts |
| **Easy to test** | You can inject mock objects for unit testing |
| **Better maintainability** | Clean, modular code that's easy to change |
| **Automatic object management** | Spring handles the full lifecycle — no manual cleanup needed |

---

[← Back to Index](../README.md) | [Next Chapter →](chapter-2-spring-mvc.md)
