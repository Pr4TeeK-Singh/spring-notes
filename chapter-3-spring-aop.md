# Chapter 3 — Spring AOP

[← Back to Index](../README.md)

---

## What is Spring AOP?

**AOP** stands for **Aspect Oriented Programming**.

It is used to add **extra functionality** to your program **without touching or changing the main business code**.

### The Problem Without AOP

Imagine you need to **log** every time a method runs. Without AOP you'd have to add logging code to EVERY method:

```java
public void saveStudent(Student s) {
    System.out.println("Saving student...");   // logging — repeated everywhere
    // actual business logic
    studentRepo.save(s);
    System.out.println("Student saved.");      // logging — repeated everywhere
}

public void deleteStudent(int id) {
    System.out.println("Deleting student...");  // same logging again!
    studentRepo.delete(id);
    System.out.println("Student deleted.");
}
```

This is **duplicate code** scattered across the entire application — messy and hard to maintain.

### The Solution with AOP

Write the logging code **once in one place** (an Aspect), and Spring applies it automatically wherever you want.

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore() {
        System.out.println("Method is about to run...");
    }

    @After("execution(* com.example.service.*.*(..))")
    public void logAfter() {
        System.out.println("Method has finished.");
    }
}
```

Now every method in the service layer automatically gets logged — **no changes to business code needed**.

---

## Cross-Cutting Concerns

Tasks that apply to **many parts** of an application are called **cross-cutting concerns**.

| Cross-Cutting Concern | Example |
|-----------------------|---------|
| **Logging** | Log every method call |
| **Security** | Check if user is logged in before running a method |
| **Transaction management** | Start/commit/rollback database transactions |
| **Error handling** | Catch and handle errors across the app |
| **Performance tracking** | Measure how long each method takes |

> **Key idea:** These tasks are needed everywhere — AOP lets you handle them in one place instead of copy-pasting the same code all over.

---

## Important Terms in Spring AOP

### Aspect
An **Aspect** is a class that contains the cross-cutting logic.

```java
@Aspect
@Component
public class LoggingAspect {
    // all logging logic goes here
}
```

> **Analogy:** An Aspect is like a **rule** — "Whenever method X runs, also do Y."

---

### Advice
**Advice** is the **actual action** that runs at a specific time.

It defines **what to do** and **when to do it**.

| Advice Type | When it runs |
|-------------|-------------|
| `@Before` | **Before** the method runs |
| `@After` | **After** the method runs (always) |
| `@AfterReturning` | After the method runs **successfully** |
| `@AfterThrowing` | After the method **throws an exception** |
| `@Around` | **Both before and after** — full control |

```java
@Before("execution(* com.example.service.*.*(..))")
public void beforeAdvice() {
    System.out.println("Before method runs");
}

@After("execution(* com.example.service.*.*(..))")
public void afterAdvice() {
    System.out.println("After method runs");
}

@Around("execution(* com.example.service.*.*(..))")
public Object aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("Before");
    Object result = pjp.proceed();  // actually runs the method
    System.out.println("After");
    return result;
}
```

---

### JoinPoint
A **JoinPoint** is a **specific point in the program** where an Aspect can be applied.

In Spring AOP, joinpoints are always **method executions**.

```java
public void beforeAdvice(JoinPoint joinPoint) {
    System.out.println("Running before: " + joinPoint.getSignature().getName());
}
```

---

### Pointcut
A **Pointcut** is an **expression that defines WHICH methods** the Advice should apply to.

```java
// Apply to ALL methods in the service package
"execution(* com.example.service.*.*(..))"

// Apply to a specific method
"execution(* com.example.service.StudentService.saveStudent(..))"
```

---

### Summary of AOP Terms

| Term | Simple meaning |
|------|---------------|
| **Aspect** | The class that holds the extra logic |
| **Advice** | What to do (the actual code that runs) |
| **JoinPoint** | Where it can be applied (a method) |
| **Pointcut** | Which methods to apply it to (the filter) |

---

## Complete AOP Example

```java
// Service class — NO logging code here at all
@Service
public class StudentService {

    public void saveStudent(Student s) {
        System.out.println("Saving student: " + s.getName());
    }

    public void deleteStudent(int id) {
        System.out.println("Deleting student: " + id);
    }
}

// Aspect class — ALL logging in one place
@Aspect
@Component
public class LoggingAspect {

    // Run before any method in StudentService
    @Before("execution(* com.example.service.StudentService.*(..))")
    public void logBefore(JoinPoint jp) {
        System.out.println(">>> Starting: " + jp.getSignature().getName());
    }

    // Run after any method in StudentService
    @After("execution(* com.example.service.StudentService.*(..))")
    public void logAfter(JoinPoint jp) {
        System.out.println("<<< Finished: " + jp.getSignature().getName());
    }
}
```

Output when `saveStudent()` is called:
```
>>> Starting: saveStudent
Saving student: John
<<< Finished: saveStudent
```

---

## Advantages of Spring AOP

| Advantage | Description |
|-----------|-------------|
| **Reduces duplicate code** | Write cross-cutting logic once, apply everywhere |
| **Separation of concerns** | Business logic stays clean — no logging/security mixed in |
| **Easier to maintain** | Change logging or security in one place — affects the whole app |
| **Non-invasive** | Your main code doesn't even know AOP is running |

---

[← Previous Chapter](chapter-2-spring-mvc.md) | [Back to Index](../README.md) | [Next Chapter →](chapter-4-spring-security.md)
