# Raul Souto - DSE - Test - Java Developer / May 27 2025.

### Get all possible anagrams of a string
```java
//Java Function: All possible anagrams of a string

    public static List<String> getAllAnagrams(String str) {
        // Basic validation
        //Validate that the input string is not null or empty and contains only letters
        if (str == null || str.isEmpty()) {
            throw new IllegalArgumentException("Input string must not be null or empty.");
        }
        if (!str.matches("[a-zA-Z]+")) {
            throw new IllegalArgumentException("Input string must contain only letters.");
        }

        // Initialize the result list with an empty string
        Set<String> result = new HashSet<>();
        result.add("");

        for (char c : str.toCharArray()) {
            Set<String> newResult = new HashSet<>();
            
            //for each string s in the current in result set
            for (String s : result) { 
                //Inserts the new character c at every possible position
                for (int i = 0; i <= s.length(); i++) {
                    //new String is created with c inserted and new anagram is saved
                    String newAnagram = s.substring(0, i) + c + s.substring(i);
                    newResult.add(newAnagram);
                }
            }
            // Update the result set with the new anagrams generated
            result = newResult;
        }
        // Convert the result set to a list and return it
        return new ArrayList<>(result);
    }



//Test Cases:

    //Test case: Valid input string "abc"
    @Test
    public void testValidInput() {
        //Set up expected anagrams for the input "abc"
        List<String> expected = Arrays.asList("abc", "acb", "bac", "bca", "cab", "cba");
        //Call the getAllAnagrams method
        List<String> result = AnagramGenerator.getAllAnagrams("abc");
        //Assert that the result contains all expected anagrams
        assertTrue(result.containsAll(expected) && expected.containsAll(result));
    }

    //Test case: Single character input "a"
    @Test
    public void testSingleCharacter() {
        //Set up expected anagrams for the input "a"
        List<String> expected = Arrays.asList("a");
        //Call the getAllAnagrams method
        List<String> result = AnagramGenerator.getAllAnagrams("a");
        //Assert that the result contains one anagram "a"
        assertEquals(expected, result);
    }

    //Test case: empty input
    @Test
    public void testEmptyInput() {
        try {
            //Call the getAllAnagrams method with an empty string
            AnagramGenerator.getAllAnagrams("");
            //If no exception is thrown, the test should fail
            fail("Expected IllegalArgumentException for empty input.");
        } catch (IllegalArgumentException e) {
            //Assert that the exception message is as expected
            assertEquals("Input string must not be null or empty.", e.getMessage());
        }
    }

    //Test case: invalid characters in input
    @Test
    public void testInvalidCharacters() {
        try {
            //Call the getAllAnagrams method with a string containing invalid characters
            AnagramGenerator.getAllAnagrams("abc123");
            //If no exception is thrown, the test should fail
            fail("Expected IllegalArgumentException for invalid characters.");
        } catch (IllegalArgumentException e) {
            //Assert that the exception message is as expected
            assertEquals("Input string must contain only letters.", e.getMessage());
        }
    }

    //Test case: Duplicate characters in input
    @Test
    public void testDuplicateCharacters() {
        //Call the getAllAnagrams method with a string containing duplicate characters
        List<String> result = AnagramGenerator.getAllAnagrams("aa");
        //Assert that the result contains only one unique anagram "aa"
        assertEquals(Arrays.asList("aa"), result); // Handles duplicate characters gracefully
    }
```
### 1 - When to override equals() method in Java
If creating a Person class for a system that manages users by name and national ID, and I want two Person objects to be considered equal if their nationalId values are the same, even if they're different objects in memory.

By default, java useses Object.equals() method, which compares memory addresses, not values of fields, so for this case, overringind equals() method is necessary.

In order to make it logical equality, we also need to override hashCode() method, so that two objects that are equal according to equals() method will have the same hash code.
```java
public class Person {
    private String name;
    private String nationalId;

    public Person(String name, String nationalId) {
        this.name = name;
        this.nationalId = nationalId;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Person person = (Person) obj;
        return nationalId.equals(person.nationalId);
    }

    @Override
    public int hashCode() {
        return nationalId.hashCode();
    }
}

```

### 2 - How to use design patter to decouple code from a third-party library which might be replaced in the future.
A design pattern to decouple code from a third-party library is the **Adapter Pattern**. This pattern allows you to create a wrapper around the third-party library, providing a consistent interface that your application can use. 

+Advantages of using the Adapter Pattern include:
- Decouples your code from third-party changes (laywer of abstraction between your code and the library).
- Makes it easier to replace the library in the future without affecting the rest of your codebase.
- Clean separation of concerns, as the adapter handles and translates the specifics of the third-party library.
- Makes it easier to unit test via interfaces.

-Disadvantages include:
- Adds boilerplate code, as you need to create an adapter class.
- May introduce performance overhead due to the additional layer of abstraction.
- Doesn't simplify the complexity of the third-party library itself, just abstracts it.

As example, imagine we are using the SendGrid API to send emails, but we want to have those advantages I mentioned ealier.

First, we define our own interface for sending emails:
```java
public interface EmailService {
    void sendEmail(String to, String subject, String body);
}
```
Next, we create an adapter that implements this interface and uses the SendGrid library:
```java
public class SendGridEmailAdapter implements EmailService {
    private final SendGridClient sendGridClient;

    public SendGridEmailAdapter(SendGridClient client) {
        this.sendGridClient = client;
    }

    @Override
    public void sendEmail(String to, String subject, String body) {
        sendGridClient.send(new SendGridEmail(to, subject, body));
    }
}
```
Finally, we can use the `NotificationService` interface in our application code without worrying about the specifics of the SendGrid library:
```java
public class NotificationService {
    private final EmailService emailService;

    public NotificationService(EmailService emailService) {
        this.emailService = emailService;
    }

    public void notifyUser(String email, String msg) {
        emailService.sendEmail(email, "Important Notification", msg);
    }
}
```
### 3 - My experience with Angular
My professional experience has primarily been with React, where I’ve built modular, reusable components and implemented features like routing, state management, and component communication using props and callbacks.

React and Angular share many similar tools and concepts, such as component-based architecture and structured data flow. While my experience is in React, I’ve studied Angular and, based on that, I’m confident I can contribute to an Angular frontend from day one.

One concept I’ve used frequently in React is parent-child communication. In Angular, this is achieved using @Input() and @Output() decorators. Here's a simple example:

`task-item.component.ts` (Child Component)
```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-task-item',
  template: `
    <div>
      {{ task }}
      <button (click)="completeTask()">Complete</button>
    </div>
  `
})
export class TaskItemComponent {
  @Input() task: string = '';
  @Output() onComplete = new EventEmitter<string>();

  completeTask() {
    this.onComplete.emit(this.task);
  }
}
```

`task-list.component.ts` (Parent Component)
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-task-list',
  template: `
    <app-task-item 
      *ngFor="let t of tasks" 
      [task]="t" 
      (onComplete)="handleComplete($event)">
    </app-task-item>
  `
})
export class TaskListComponent {
  tasks = ['Write report', 'Send emails', 'Fix bug'];

  handleComplete(task: string) {
    console.log('Completed task:', task);
  }
}
```

### 4 - Techniques to prevent SQL injection attacks in web application
To prevent SQL injection attacks in web applications, I follow a set of best practices focused on parameterized queries, ORM usage, and defensive database configurations.

1. **Parameterized Queries**: Always use parameterized queries or prepared statements to separate SQL code from data. This ensures that user input is treated as data, not executable code.
   ```java
   String sql = "SELECT * FROM users WHERE username = ? AND password = ?";
   PreparedStatement pstmt = connection.prepareStatement(sql);
   pstmt.setString(1, username);
   pstmt.setString(2, password);
   ResultSet rs = pstmt.executeQuery();
   ```
2. **ORM Frameworks**: Use Object-Relational Mapping (ORM) frameworks like Hibernate/JPA, which automatically handle parameterization and reduce the risk of SQL injection.
   ```java
   @Query("SELECT u FROM User u WHERE u.email = :email")
    User findByEmail(@Param("email") String email);
    ```
3. **Input Validation**: Even though parameterization protects from injection, I validate inputs for expected patterns and lengths utilizing DTOs and validation annotations.
   ```java
   @NotBlank
   @Size(max = 50)
   private String username;
   ```
4. **Least Privilege Principle**: Configure database user permissions to limit access to only what is necessary for the application. Avoid using admin-level accounts for application database access.
5. **SQL Error Suppression**: Avoid exposing SQL error details to users. Errors are logged internally and sanitized in API responses.

### 5 - Improving performance of a batch process that interacts with a database and an FTP server.
1. Identify Bottlenecks: 
- Use profiling tools to identify slow queries, network latency, and file transfer times like the Java VisualVM, Java Flight Recorder, or Spring Boot Actuator.
- Analyze logging and metrics to pinpoint where the process is spending most of its time.
- Use EXPLAIN in SQL or monitor logs to detect expensive joins or full table scans.
3. Optimize Database Interactions:
- Ensure filtering/join conditions use indexed columns.
- Avoid N+1 queries by prefetch related data where needed (e.g., customer and orders).
- Use batch processing for inserts/updates to reduce round trips to the database.
4. Optimize FTP Interactions - I haven't worked with FTP servers, but after researching about it, I would consider:
- Compress files before transfering to reduce size and transfer time.
- Use multi-threading to parallelize file transfers if the FTP server supports it.
- Implement retry logic for failed transfers to avoid manual intervention.
- Use a library like Apache Commons Net for efficient FTP operations.
```java
// Example of using Apache Commons Net for FTP operations
FTPClient ftpClient = new FTPClient();
ftpClient.connect("ftp.example.com");
ftpClient.login("user", "pass");
ftpClient.enterLocalPassiveMode();

FileOutputStream out = new FileOutputStream("local.csv");
ftpClient.retrieveFile("/remote/file.csv", out);
out.close();

ftpClient.logout();
ftpClient.disconnect();
```

### 6 - Write SQL Query for given tables:
a. Returns the names of all Salesperson that don’t have any order with Samsonic.
```sql
SELECT s.Name
FROM Salesperson s
WHERE s.ID NOT IN (
    SELECT o.salesperson_id
    FROM Orders o
    JOIN Customer c ON o.customer_id = c.ID
    WHERE c.Name = 'Samsonic'
);
```
b. Updates the names of Salesperson that have 2 or more orders. It’s necessary to add an ‘*’ in the end of the name. 
```sql
UPDATE Salesperson
SET Name = CONCAT(Name, '*')
WHERE ID IN (
    SELECT salesperson_id
    FROM Orders
    GROUP BY salesperson_id
    HAVING COUNT(*) >= 2
);
```
c. Deletes all Salesperson that placed orders to the city of Jackson.
```sql
DELETE FROM Salesperson
WHERE ID IN (
    SELECT DISTINCT o.salesperson_id
    FROM Orders o
    JOIN Customer c ON o.customer_id = c.ID
    WHERE c.City = 'Jackson'
);
```
d. The total sales amount for each Salesperson. If the salesperson hasn't sold anything show zero.
```sql
SELECT s.Name, COALESCE(SUM(o.Amount), 0) AS total_sales
FROM Salesperson s
LEFT JOIN Orders o ON s.ID = o.salesperson_id
GROUP BY s.Name;
```

### 7 - System XYZ design
1. Use case:

- As an admin or regular user, I want to be able to create, update, and search for plant entries in the XYZ system, so that I can manage plant data efficiently while ensuring data integrity and access control.

- As an admin user, I want to be the only one able to delete plant entries, so that we maintain proper control and avoid accidental loss of important data.

2. Business rules and Assumptions:

- Each plant must have a numeric-only, mandatory, and unique code.
- Each plant can have an optional description, which must be alphanumeric and up to 10 characters.
- Only users with "admin" role are authorized to delete plant entries.
- Duplicate plant codes must be prevented across the system.
- Assumption: The system has user authentication and roles (admin vs regular user).
- Assumption: All plant operations are logged for auditing in future phases.

3. Validations and Security Measures:
#### Validation
- Use DTOs (e.g., PlantRequestDto) to isolate and control incoming data from users.
- Use Java Bean Validation (JSR-380) annotations like:
```java
public class PlantRequestDto {
    @NotNull
    @Pattern(regexp = "\\d+", message = "Code must be numeric only")
    private String code;

    @Size(max = 10, message = "Description must be at most 10 characters")
    @Pattern(regexp = "[a-zA-Z0-9]*", message = "Description must be alphanumeric")
    private String description;
}
```
#### Security Measures
- Role-based Access Control (RBAC):
    - only admins can call the delete endpoint (enforced via backend security filters)
- Input validation to avoid SQL injection or malformed data
- Database constraints:
    - Unique index on the code field.
    - Column length restriction for description
- Audit trail for all CRUD operations:
    - Log every create, update, and delete operation with user ID and timestamp

4. Testing cases:
#### Positive Test Cases
- Create a plant with valid numeric code and optional description
- Update plant's description
- Delete a plant as admin
- Search for plant by code or description

#### Negative/Eedge Test Cases
- Try creating two plants with the same code: should fail.
- Create a plant with:
    - Non-numeric code → validation error
    - Description longer than 10 characters → validation error
    - Special characters in description: validation error
- Try deleting a plant as a non-admin: should be forbidden
- Update code to a value that already exists: should be rejected
- Submit a blank code or null: should be rejected
---
### 8 - Testing User Registration
#### 1. Types of tests
#### Unit Tests:
- Validate that a user with valid fields is saved successfully.
- Ensure duplicate email triggers a specific exception.
- Check that only ADMIN users can invoke the delete operation.

#### Integration Tests:
Verify the end-to-end behavior of service + database + security.
- Create a user and retrieve it from the DB.
- Attempt deletion with different user roles (admin vs non-admin).
- Ensure unique email constraint is respected across DB layer.

#### End-to-End Tests:
Simulate actual user interactions.
- Fill form and submit: verify success/failure alerts.
- Try deleting a user as a regular user: verify "permission denied" response.
- Edit user info and confirm changes reflect in UI.

#### 2. Edge Cases and How to Handle them:
- **Missing required fields(name, email):** Use DTO validation: @NotNull, @NotBlank
- **Invalid email format:** Use regex validation with @Email
- **Duplicate email:** Check in service layer and enforce DB constraint
- **Deletion by non-admin:** Restrict via RBAC (@PreAuthorize)

#### 3. Example Test Cases:
#### Creating a user with duplicate email
```java
@Test
void shouldNotAllowDuplicateEmail() {
    // Given
    UserRequestDto user1 = new UserRequestDto("Alice", "alice@email.com", "123 Street", "555-1111");
    UserRequestDto user2 = new UserRequestDto("Bob", "alice@email.com", "456 Avenue", "555-2222");

    userService.createUser(user1);
    
    // When + Then
    assertThrows(DuplicateEmailException.class, () -> {
        userService.createUser(user2);
    });
}
```
#### Deleting a user as a non-admin
```java
@Test
@WithMockUser(username = "john", roles = {"USER"})
void nonAdminCannotDeleteUser() throws Exception {
    mockMvc.perform(delete("/users/123"))
           .andExpect(status().isForbidden()); // 403
}
```
