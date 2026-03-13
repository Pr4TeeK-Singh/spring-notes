# Chapter 2 — Spring MVC

[← Back to Index](../README.md)

---

## What is Spring MVC?

**Spring MVC** is the part of Spring used to build **web applications**.

**MVC** stands for **Model – View – Controller**. It splits a web application into 3 separate parts so the code stays **clean, organized, and easy to manage**.

> **Simple flow:** User fills a form → **Controller** receives the data → **Model** processes it → **View** shows the result.

---

## MVC Architecture

### Model
The **Model** holds the **data and business logic** of the application.

- Communicates with the database.
- Processes and stores data.
- Does NOT care about how data is displayed.

```
Examples: User data, Product data, Order data
```

---

### View
The **View** is the **UI** — what the user sees on screen.

- Takes data from the Model and displays it.
- Does NOT contain business logic.

```
Examples: JSP pages, HTML pages, Thymeleaf templates
```

---

### Controller
The **Controller** handles **user requests**.

- Receives input from the user (form submissions, button clicks, URL requests).
- Decides what to do with it.
- Calls the Model → gets data → passes it to the View.

```
Example: User clicks "Login" → Controller processes the login request
```

---

## How Spring MVC Works (Flow)

```
Browser
   |
   | 1. User sends request (e.g. /login)
   ↓
DispatcherServlet          ← Front Controller (receives ALL requests)
   |
   | 2. Finds the right Controller
   ↓
Controller                 ← Handles the request, calls business logic
   |
   | 3. Interacts with Model (data/service layer)
   ↓
Model                      ← Processes data, returns result
   |
   | 4. Controller sends data + view name to DispatcherServlet
   ↓
DispatcherServlet
   |
   | 5. Sends to View (e.g. home.html)
   ↓
View                       ← Generates the final HTML page
   |
   | 6. Response sent back to browser
   ↓
Browser                    ← User sees the result
```

### Step-by-Step

| Step | What happens |
|------|-------------|
| 1 | User sends a request from the browser (e.g. clicks a link or submits a form) |
| 2 | **DispatcherServlet** receives the request |
| 3 | DispatcherServlet finds and calls the correct **Controller** |
| 4 | Controller processes the request and interacts with the **Model** |
| 5 | Controller sends data to the **View** |
| 6 | View generates the final HTML page |
| 7 | Response is sent back to the browser |

---

## DispatcherServlet

The **DispatcherServlet** is the **most important component** of Spring MVC.

- It works as the **Front Controller** — ALL requests come through it first.
- It decides which Controller should handle each request.
- It coordinates the entire flow from request to response.

```
User requests /login
       ↓
DispatcherServlet receives it
       ↓
Sends it to LoginController
       ↓
LoginController handles it and returns a response
```

> **Analogy:** DispatcherServlet is like a **receptionist** — every visitor (request) comes to the receptionist first, and the receptionist routes them to the right department (Controller).

---

## Controller in Spring MVC

A **Controller** handles HTTP requests from users.

Created using the `@Controller` annotation.

```java
@Controller
public class HomeController {

    @GetMapping("/home")                    // handles GET /home
    public String showHomePage(Model model) {
        model.addAttribute("message", "Welcome to Spring MVC!");
        return "home";                      // returns view name "home"
    }
}
```

### Common Annotations in Controllers

| Annotation | What it does |
|------------|-------------|
| `@Controller` | Marks the class as a Spring MVC controller |
| `@RestController` | Like `@Controller` but for REST APIs (returns JSON/XML) |
| `@GetMapping("/path")` | Handles HTTP GET requests |
| `@PostMapping("/path")` | Handles HTTP POST requests |
| `@RequestParam` | Reads a parameter from the URL |
| `@PathVariable` | Reads a value from the URL path |
| `@RequestBody` | Reads the request body (for REST APIs) |

```java
@Controller
public class StudentController {

    @GetMapping("/student/{id}")
    public String getStudent(@PathVariable int id, Model model) {
        // fetch student by id and add to model
        model.addAttribute("student", studentService.findById(id));
        return "studentDetail";             // show studentDetail.html
    }

    @PostMapping("/student/add")
    public String addStudent(@RequestParam String name, Model model) {
        // save the student
        model.addAttribute("message", "Student " + name + " added!");
        return "confirmation";
    }
}
```

---

## Advantages of Spring MVC

| Advantage | Description |
|-----------|-------------|
| **Clean separation** | Model, View, and Controller are completely separated — easy to manage |
| **Easy to maintain** | Change one part without affecting others |
| **Supports REST APIs** | Build REST APIs easily with `@RestController` |
| **Flexible views** | Works with JSP, Thymeleaf, HTML, JSON, etc. |
| **Integration** | Works seamlessly with Spring Core, Spring Security, Spring Data |

---

[← Previous Chapter](chapter-1-spring-core.md) | [Back to Index](../README.md) | [Next Chapter →](chapter-3-spring-aop.md)
