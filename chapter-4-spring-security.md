# Chapter 4 — Spring Security

[← Back to Index](../README.md)

---

## What is Spring Security?

**Spring Security** is a framework used to **secure Java web applications**.

It protects your application from **unauthorized access** — making sure only the right people can access the right things.

> **Simple analogy:** Think of Spring Security as a **security guard** at the entrance of a building. It checks your ID (authentication) and decides which floors you're allowed to enter (authorization).

---

## Authentication vs Authorization

These are the two core concepts of Spring Security — people often confuse them.

| Concept | Question it answers | Example |
|---------|-------------------|---------|
| **Authentication** | *Who are you?* | Login with username + password |
| **Authorization** | *What are you allowed to do?* | Admin can delete; user can only view |

---

### Authentication

**Authentication** = **verifying the identity** of a user.

It answers: *"Are you really who you say you are?"*

```
User visits /dashboard
       ↓
Spring Security checks: Is the user logged in?
       ↓
If NO → redirect to /login
If YES → allow access
```

```java
// User provides credentials
Username: john@example.com
Password: mypassword123

// Spring Security verifies them against the database
// If correct → user is authenticated (logged in)
// If wrong   → login fails
```

---

### Authorization

**Authorization** = **checking what a user is allowed to do** after they've logged in.

It answers: *"You're logged in — but are you allowed to access this?"*

```
Authenticated user visits /admin/deleteUser
       ↓
Spring Security checks: Does this user have ADMIN role?
       ↓
If YES → allow access
If NO  → return 403 Forbidden
```

### Role-Based Access Control Example

| Role | What they can do |
|------|-----------------|
| **ADMIN** | Create users, delete data, view all pages |
| **USER** | View their own data only |
| **GUEST** | View public pages only |

---

## Security Configuration

A security configuration class defines the **rules** for your application:

- Which pages require login
- Which pages are public (anyone can see)
- What roles are needed for specific pages

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()       // anyone can access
                .requestMatchers("/admin/**").hasRole("ADMIN")   // admin only
                .anyRequest().authenticated()                    // everything else needs login
            )
            .formLogin(form -> form
                .loginPage("/login")                             // custom login page
                .permitAll()
            )
            .logout(logout -> logout
                .permitAll()
            );

        return http.build();
    }
}
```

---

## Password Protection

Spring Security **never stores passwords as plain text** — that would be a huge security risk.

Passwords are stored as a **hash** (a scrambled version that can't be reversed).

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();  // BCrypt is the recommended encoder
}

// When saving a new user's password
String rawPassword = "mypassword123";
String encodedPassword = passwordEncoder.encode(rawPassword);
// stored in DB as: $2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy

// When user logs in — Spring compares automatically
passwordEncoder.matches("mypassword123", encodedPassword);  // true
```

---

## Login and Logout

Spring Security provides **ready-made login and logout** functionality out of the box.

```java
// Minimum config to get a login form working
http
    .formLogin(Customizer.withDefaults())  // Spring generates a default login page
    .logout(Customizer.withDefaults());    // /logout endpoint created automatically
```

### Custom Login Page

```java
.formLogin(form -> form
    .loginPage("/login")           // your custom login HTML page
    .loginProcessingUrl("/login")  // where the form POSTs to
    .defaultSuccessUrl("/home")    // where to go after successful login
    .failureUrl("/login?error")    // where to go if login fails
)
```

---

## Protecting Pages — Example

```java
// Only logged-in users can access /dashboard
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/dashboard").authenticated()
    .requestMatchers("/").permitAll()        // homepage is public
);
```

```
Not logged in → visits /dashboard
→ Spring Security redirects to /login

Logs in successfully
→ Spring Security redirects back to /dashboard
→ Page loads normally
```

---

## Common Annotations in Spring Security

| Annotation | Where to use | What it does |
|------------|-------------|-------------|
| `@EnableWebSecurity` | Config class | Enables Spring Security |
| `@PreAuthorize` | Method | Checks role/permission before method runs |
| `@Secured` | Method | Restricts method to specific roles |
| `@RolesAllowed` | Method | Same as `@Secured` (standard Java annotation) |

```java
@RestController
public class AdminController {

    @GetMapping("/admin/users")
    @PreAuthorize("hasRole('ADMIN')")           // only ADMIN can call this
    public List<User> getAllUsers() {
        return userService.findAll();
    }

    @DeleteMapping("/admin/user/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public void deleteUser(@PathVariable int id) {
        userService.delete(id);
    }
}
```

---

## Advantages of Spring Security

| Advantage | Description |
|-----------|-------------|
| **Secure authentication** | Industry-standard login system — hard to break |
| **Easy Spring Boot integration** | Add one dependency and security is set up automatically |
| **Supports JWT and OAuth2** | Works with modern token-based and social login systems |
| **Role-based access control** | Fine-grained control over who can do what |
| **Protection from attacks** | Guards against CSRF, session fixation, clickjacking, etc. |
| **Highly customizable** | Override any part to fit your needs |

---

## Quick Summary

```
User visits protected page
        ↓
Spring Security intercepts the request
        ↓
Authentication check → Is user logged in?
    NO  → redirect to /login
    YES → continue
        ↓
Authorization check → Does user have required role?
    NO  → 403 Forbidden
    YES → allow access to page
```

---

[← Previous Chapter](chapter-3-spring-aop.md) | [Back to Index](../README.md)
