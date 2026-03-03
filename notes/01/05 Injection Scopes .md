## Injection Scopes

In NestJS, the final answer is that **providers are Singletons by default**. 

However, NestJS provides a robust system called **Injection Scopes** that allows you to change this behavior based on your architectural needs. There are three primary scopes:

---

### 1. DEFAULT (Singleton Scope)
This is the standard behavior. A single instance of the provider is instantiated when the application bootstraps and is shared across the entire application.
*   **Lifecycle:** Created once, lives as long as the application process is running.
*   **Performance:** Highly efficient. Memory is allocated once, and the same instance is reused for every request.
*   **State:** Since it's a singleton, if you store data in a class property, that data is shared across every user and every request (which is usually avoided in stateless APIs).

### 2. REQUEST (Request Scope / "Per-call")
A new instance of the provider is created exclusively for each incoming **HTTP request**. 
*   **Lifecycle:** Created when a request is received; garbage collected after the response is sent.
*   **Use Case:** Ideal when you need to access request-specific information (like a JWT payload or a unique Request-ID) deep within your service layer without passing it through every function argument.
*   **Warning (The "Bubbling" Effect):** If a Singleton-scoped Controller injects a Request-scoped Service, the **Controller itself becomes Request-scoped**. Scope "bubbles up" to any component that consumes it.

### 3. TRANSIENT (Transient Scope)
A new, dedicated instance of the provider is created for every component that injects it.
*   **Lifecycle:** If Service A and Service B both inject "LoggerService" (marked as Transient), they each get their own completely separate instance of LoggerService.
*   **Use Case:** Useful for "utility" classes that might hold a tiny bit of configuration state specific to the consumer, but don't need to live for the duration of a request.

---

### How to Define the Scope
You define the scope within the `@Injectable()` decorator using the `scope` property:

```typescript
import { Injectable, Scope } from '@nestjs/common';

@Injectable({ scope: Scope.REQUEST }) // Or Scope.TRANSIENT / Scope.DEFAULT
export class MyService {
  // Logic here...
}
```

### Performance Comparison: Singleton vs. Request Scope
| Feature | Singleton (Default) | Request Scope |
| :--- | :--- | :--- |
| **Instantiation** | Once at startup | Once per HTTP request |
| **Memory Usage** | Low (Stable) | Higher (Churn during high traffic) |
| **Speed** | Faster (No overhead) | Slower (Overhead of creating instances) |
| **Shared State** | Global to app | Local to one request |

### Summary Recommendation
In NestJS development, you should **always prefer Singletons** unless you have a specific requirement that mandates Request scope. Because NestJS is built on Node.js (which is single-threaded), managing thousands of class instantiations per second under high load can lead to performance degradation if not strictly necessary.
