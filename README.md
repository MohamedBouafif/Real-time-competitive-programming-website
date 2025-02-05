# Real-time-competitive-programming-website
# **Codeforces Clone Project Roadmap**

## **Project Overview**

We are building a Codeforces-like platform to host competitive programming competitions at our university. The platform will include:

- A **home page** with active competitions and recent problems.
- A **problem set** where users can view and solve problems.
- A **competition system** to host real-time programming contests.
- A **submission system** to evaluate user code and provide results.

This document outlines the step-by-step roadmap for the project, including tasks, required knowledge, and tools.

---

## **Phase 1: Project Setup & Core Concepts**

**Goal**: Build the foundation of the Spring Boot project.

### **Tasks**

1. **Learn Spring Boot Basics**:
   - Create a simple REST API (e.g., a "Hello World" endpoint).
   - Understand `@RestController`, `@GetMapping`, `@PostMapping`.
   - Use **Spring Initializr** to generate a project with:
     - Dependencies: Spring Web, Spring Data JPA, Lombok, PostgreSQL Driver.
   - *Resource*: [Spring Boot Quickstart Guide](https://spring.io/quickstart).

2. **Set Up Version Control**:
   - Create a GitHub repository.
   - Use Git for collaboration (branches, pull requests).

3. **Design the Database Schema**:
   - Sketch tables for:
     - **Users** (id, username, password, role).
     - **Problems** (id, title, description, difficulty, test cases).
     - **Competitions** (id, name, start_time, end_time, problems).
     - **Submissions** (id, user_id, problem_id, code, verdict, timestamp).
   - *Tool*: Use [dbdiagram.io](https://dbdiagram.io/) to visualize relationships.

4. **Connect to a Database**:
   - Configure `application.properties` for PostgreSQL:

     ```properties
     spring.datasource.url=jdbc:postgresql://localhost:5432/codeforces_clone
     spring.datasource.username=postgres
     spring.datasource.password=root
     spring.jpa.hibernate.ddl-auto=update
     ```
   - Create JPA entities (e.g., `User`, `Problem` classes with `@Entity`).

---

## **Phase 2: User Authentication & Authorization**

**Goal**: Secure the application with user roles (admins, contestants).

### **Tasks**

1. **Implement JWT Authentication**:
   - Add `Spring Security` and `jjwt` dependencies.
   - Create:
     - `AuthController` (register, login endpoints).
     - `JwtUtil` class to generate/validate tokens.
     - `UserDetailsService` to load users from the database.
   - *Guide*: [Spring Boot + JWT Tutorial](https://www.bezkoder.com/spring-boot-jwt-authentication/).

2. **Role-Based Access Control**:
   - Add a `role` field to the `User` entity (e.g., `ADMIN`, `USER`).
   - Use `@PreAuthorize("hasRole('ADMIN')")` to restrict admin-only endpoints (e.g., creating problems).

3. **Test with Postman**:
   - Create test users and verify login returns a JWT token.

---

## **Phase 3: Problem Management**

**Goal**: Allow admins to create problems and users to view them.

### **Tasks**

1. **Problem CRUD API**:
   - Create endpoints:
     - `POST /api/problems` (admin-only: add a problem with test cases).
     - `GET /api/problems` (public: list all problems).
     - `GET /api/problems/{id}` (public: view problem details).
   - Use DTOs (Data Transfer Objects) for requests/responses.
   - *Example*:

     ```java
     @PostMapping
     @PreAuthorize("hasRole('ADMIN')")
     public Problem createProblem(@RequestBody ProblemDTO problemDTO) {
         // Convert DTO to Problem entity and save to DB
     }
     ```

2. **Test Case Storage**:
   - Store test cases in the database (input, expected output, time limit).
   - Use a `@OneToMany` relationship between `Problem` and `TestCase` entities.

3. **Frontend Integration** (Optional for MVP):
   - Create a basic frontend (React/Thymeleaf) to display problems.

---

## **Phase 4: Competition System**

**Goal**: Schedule competitions and allow users to participate.

### **Tasks**

1. **Competition CRUD API**:
   - Create `Competition` entity with:
     - Start/end times.
     - List of linked problems (`@ManyToMany` with `Problem`).
     - Participants (`@ManyToMany` with `User`).
   - Endpoints:
     - `POST /api/competitions` (admin: schedule a competition).
     - `GET /api/competitions` (public: list upcoming/running competitions).

2. **Competition Registration**:
   - Allow users to register for a competition (`POST /api/competitions/{id}/register`).

3. **Competition Timer**:
   - Use Spring’s `@Scheduled` to automatically start/end competitions.
   - Example:

     ```java
     @Scheduled(fixedRate = 60000) // Check every minute
     public void updateCompetitionStatus() {
         // Update competitions from "UPCOMING" to "RUNNING" to "ENDED"
     }
     ```

---

## **Phase 5: Submission & Grading System**

**Goal**: Handle code submissions and return verdicts (e.g., "Accepted", "Wrong Answer").

### **Tasks**

1. **Submission API**:
   - Endpoint: `POST /api/submit` (accepts code, problem ID, competition ID).
   - Store submissions in the database with a "PENDING" status.

2. **Code Execution**:
   - **Option 1 (Easier)**: Use [Judge0 API](https://judge0.com/) (free hosted code execution).
   - **Option 2 (Advanced)**: Self-hosted Docker execution.

3. **Test Case Validation**:
   - For each submission, run against all test cases for the problem.
   - Compare the output with expected results.
   - Update submission verdict (e.g., "Accepted", "Time Limit Exceeded").

---

## **Phase 6: Real-Time Features**

**Goal**: Live leaderboards and competition updates.

### **Tasks**

1. **WebSocket Integration**:
   - Configure Spring WebSocket for real-time communication.
   - Broadcast leaderboard updates during competitions.
   - *Guide*: [Spring WebSocket](https://spring.io/guides/gs/messaging-stomp-websocket/).

2. **Leaderboard Logic**:
   - Calculate scores based on submissions (e.g., time taken, correctness).
   - Store scores in Redis for fast read/write.

---

## **Phase 7: Frontend Development**

**Goal**: Build user interfaces for the core pages.

### **Tasks**

1. **Home Page**:
   - Show active competitions and recent problems.
   - Use React/Thymeleaf + Bootstrap for styling.

2. **Problem Page**:
   - Display problem description and code submission form.
   - Use a code editor library (e.g., Monaco Editor for React).

3. **Competition Page**:
   - Live leaderboard (update via WebSocket).
   - Timer showing remaining competition time.


