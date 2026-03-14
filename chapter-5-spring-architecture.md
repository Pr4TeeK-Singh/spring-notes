# Chapter 5 — Spring Architecture 🏛️

> **What this chapter covers:** The full Spring Framework architecture — its layers, modules, and how they all fit together — explained in plain English.

---

## 1. What is Spring Framework?

Spring is a **powerful Java framework** that helps you build applications faster and cleaner.

Think of it like a **well-organized toolbox** 🧰 — you pick only the tools (modules) you need for your project.

**Key goals of Spring:**
- Remove boilerplate code (repetitive, boring code)
- Manage objects for you (so you don't have to)
- Make testing easy
- Connect all layers of your app cleanly

---

## 2. The Big Picture — Spring Architecture Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                     Spring Framework                         │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐ │
│  │     Web      │  │     AOP      │  │  Data Access /     │ │
│  │    Layer     │  │    Layer     │  │  Integration Layer │ │
│  │              │  │              │  │                    │ │
│  │  Spring MVC  │  │  Aspects     │  │  JDBC · ORM · JMS  │ │
│  │  WebFlux     │  │  Logging     │  │  Transactions      │ │
│  │  REST APIs   │  │  Security    │  │                    │ │
│  └──────────────┘  └──────────────┘  └────────────────────┘ │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              Core Container  (THE HEART ❤️)            │  │
│  │         Core · Beans · Context · SpEL                  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │                   Test Module 🧪                        │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

> 💡 All layers sit **on top** of the Core Container. The Core Container is the foundation everything depends on.

---

## 3. Layer 1 — Core Container (The Foundation)

This is the **most important layer**. Without this, nothing works.

| Module | Simple Explanation |
|---|---|
| **Core** | Provides IoC and DI — the basic magic of Spring |
| **Beans** | Creates and manages all your Java objects (Beans) |
| **Context** | A smart container that holds and organizes all beans |
| **SpEL** | Spring Expression Language — lets you write dynamic expressions |

### What is IoC? (Inversion of Control)

> **Normal way:** YOU create objects.  
> **Spring way:** SPRING creates objects for you.

```java
// ❌ Without Spring — manual object creation
UserService userService = new UserService();

// ✅ With Spring — Spring creates and injects it
@Autowired
UserService userService;
```

### What is Dependency Injection (DI)?

> Spring **gives your class the objects it needs** — you don't go and get them yourself.

```java
@Component
public class OrderService {

    @Autowired                     // Spring injects UserService here automatically
    private UserService userService;

}
```

---

## 4. Layer 2 — Data Access / Integration Layer

Everything related to **talking to a database or external systems**.

| Module | Simple Explanation |
|---|---|
| **JDBC** | Simplifies raw SQL queries — much less code needed |
| **ORM** | Works with Hibernate/JPA — maps Java objects to DB tables |
| **Transactions** | Auto-handles commit and rollback |
| **JMS** | Sends/receives messages between different apps |
| **OXM** | Converts Java objects to/from XML |

### Without Spring vs With Spring (JDBC example)

```java
// ❌ Without Spring — 10+ lines just to run a query
Connection conn = DriverManager.getConnection(url, user, pass);
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users");
ResultSet rs = ps.executeQuery();
// ... more boilerplate ...

// ✅ With Spring — clean and simple
List<User> users = jdbcTemplate.query("SELECT * FROM users", userMapper);
```

---

## 5. Layer 3 — Web Layer (Spring MVC)

Handles all **HTTP requests and web responses** — your controllers, REST APIs, views.

### How a Web Request Flows Through Spring:

```
🌐 Browser sends HTTP Request
        ↓
🚦 DispatcherServlet  ←  (Front Controller — single entry point for all requests)
        ↓
🗺️ Handler Mapping    ←  (Figures out which Controller to call)
        ↓
🎮 Controller         ←  (Your code — handles the request)
        ↓
⚙️ Service Layer      ←  (Business logic)
        ↓
🗄️ Repository / DAO   ←  (Database operations)
        ↓
📦 Model              ←  (Data to send back)
        ↓
🖥️ View / JSON        ←  (HTML page or REST response)
        ↓
🌐 Browser gets Response
```

### Key Components of Web Layer

| Component | Role |
|---|---|
| **DispatcherServlet** | Traffic cop — all requests go through here first |
| **Controller** | Handles what to do for a specific URL |
| **Model** | Holds the data to display |
| **View Resolver** | Finds the right HTML template to show |
| **@RestController** | Used for REST APIs (returns JSON/XML) |

---

## 6. Layer 4 — AOP (Aspect Oriented Programming)

> Add **extra behaviour** (logging, security, timing) to your app **without touching your main code**.

### Simple Analogy 📹

> A **security camera** records everyone entering a building.  
> It doesn't stop or interrupt anyone — it just runs silently alongside everything else.  
> AOP works the same way — it adds behaviour without modifying existing methods.

### Key AOP Terms

| Term | Simple Meaning |
|---|---|
| **Aspect** | The extra behaviour you want to add (e.g. logging) |
| **Advice** | When to run it — Before? After? Around? |
| **JoinPoint** | A point in your code where Aspect can be applied (e.g. a method call) |
| **Pointcut** | Which methods to apply the Aspect to |

```java
// ✅ Log every method call in the service layer — without touching service code!
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.app.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Calling: " + joinPoint.getSignature().getName());
    }
}
```

### Common Uses of AOP

- 📝 Logging method calls
- 🔒 Security and authentication checks
- ⏱️ Measuring method execution time
- 🔄 Managing database transactions
- ⚠️ Handling exceptions globally

---

## 7. Layer 5 — Test Module

Spring makes testing **clean and easy**.

| Feature | Benefit |
|---|---|
| **JUnit + Spring Test** | Test individual beans in isolation |
| **MockMvc** | Test your Controllers without starting a real server |
| **@SpringBootTest** | Load the full Spring context for integration tests |
| **@MockBean** | Replace a real bean with a fake one during testing |

```java
@SpringBootTest
class UserServiceTest {

    @Autowired
    UserService userService;

    @Test
    void testFindUser() {
        User user = userService.findById(1L);
        assertNotNull(user);
    }
}
```

---

## 8. Spring Bean Lifecycle (How Spring manages objects)

```
📄 Step 1 → Spring reads your config (@Component, @Bean, XML)
        ↓
🏗️ Step 2 → Spring CREATES the Bean (calls constructor)
        ↓
💉 Step 3 → Spring INJECTS dependencies (@Autowired fields filled)
        ↓
🔧 Step 4 → @PostConstruct runs (any setup code)
        ↓
✅ Step 5 → Bean is READY to use
        ↓
        ... app runs ...
        ↓
🗑️ Step 6 → @PreDestroy runs (cleanup code)
        ↓
💀 Step 7 → Bean is DESTROYED (app shuts down)
```

---

## 9. Spring Framework vs Spring Boot

| Feature | Spring Framework | Spring Boot |
|---|---|---|
| Configuration | Manual (XML / Java config) | Auto-configured ⚡ |
| Server setup | Set up Tomcat separately | Embedded Tomcat built-in |
| Startup speed | Slower to set up | Very fast |
| Complexity | More control, more code | Less code, faster start |
| Best for | Complex custom setups | REST APIs & microservices |

> 💡 **Simple rule:** Spring Boot = Spring Framework + Auto Config + Embedded Server

---

## 10. All Spring Modules — Quick Reference

| Layer | Modules | Purpose |
|---|---|---|
| **Core Container** | Core, Beans, Context, SpEL | IoC, DI, Bean management |
| **Data Access** | JDBC, ORM, Transactions, JMS | Database & messaging |
| **Web** | Spring MVC, WebFlux | Web apps & REST APIs |
| **AOP** | Spring AOP, AspectJ | Cross-cutting concerns |
| **Test** | Spring Test, MockMvc | Unit & integration testing |

---

## 11. Summary

| Concept | One-line Explanation |
|---|---|
| **IoC** | Spring creates objects, not you |
| **DI** | Spring gives objects what they need |
| **Core Container** | Heart of Spring — manages all beans |
| **Spring MVC** | Handles web requests and REST APIs |
| **AOP** | Adds behaviour (logging/security) without changing your code |
| **Data Layer** | Simplifies database operations |
| **Test Module** | Makes unit and integration testing easy |
| **Spring Boot** | Spring with auto-configuration — faster to start |

---

> **Key takeaway:** Spring Architecture is a set of **independent modules built on top of a Core Container**.  
> You use only what you need — that's what makes Spring so flexible and popular. 🌱

---

*Previous: [Chapter 4 — Spring Security](chapter-4-spring-security.md)*
