# Curriculum

Here is a proposed curriculum designed for an enterprise-level developer:

### **Phase 1: Getting Started & The Core Foundation**
*   **1.1 Environment Setup:** Installing the CLI and understanding the toolchain.
*   **1.2 Creating a Project:** Scaffolding the boilerplate and exploring the folder structure.
*   **1.3 Anatomy of a NestJS App:** Bootstrapping and the "Root Module."
*   **1.4 Modules & Encapsulation:** Organizing features and managing the IoC scope.
*   **1.5 Controllers:** Defining endpoints and routing.
*   **1.6 Providers & Dependency Injection:** Creating services and injection scopes.

---
### **Phase 2: The Request Pipeline (The "Dynamic" Flow)**
*   **2.1 Pipes:** Validation and Data Transformation (think Model Binding and Validation).
*   **2.2 Guards:** Authorization (similar to Action Filters/Authorize attributes).
*   **2.3 Interceptors:** Aspect-Oriented Programming (logging, transforming responses).
*   **2.4 Exception Filters:** Centralized error handling.

### **Phase 3: Data & Integration**
*   **3.1 DTOs & Schema Validation:** Using `class-validator` and `class-transformer`.
*   **3.2 Persistence:** Integrating with ORMs (TypeORM or Prisma).
*   **3.3 Configuration:** Managing environments (`@nestjs/config`).

### **Phase 4: Advanced & NestJS v11 Features**
*   **4.1 Lifecycle Hooks:** OnModuleInit, OnApplicationBootstrap.
*   **4.2 Enhanced Logging:** Utilizing the new v11 JSON logging.
*   **4.3 Performance:** Express 5 vs. Fastify 5 under the hood.
*   **4.4 Testing:** Unit testing with Jest and Integration testing with Supertest.

---
