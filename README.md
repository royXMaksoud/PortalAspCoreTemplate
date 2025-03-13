
# This project contains a alean Code Template for ASP.NET Core

## 1. Project Structure
Organize your project into a clear and modular structure.

```
/MyApp
│
├── /Controllers          # API/Web controllers
├── /Services             # Business logic and service layer
├── /Repositories         # Data access layer (if using Repository pattern)
├── /Models               # Domain models and DTOs (Data Transfer Objects)
├── /Mappings             # AutoMapper profiles or manual mappings
├── /Extensions           # Custom extension methods
├── /Middleware           # Custom middleware
├── /Filters              # Action filters
├── /Validators           # FluentValidation or custom validation logic
├── /Configuration        # Configuration classes and settings
├── /Infrastructure       # Infrastructure-related code (e.g., database context, caching)
├── /Helpers              # Utility and helper classes
├── /wwwroot              # Static files (CSS, JS, images)
├── /Properties           # Launch settings and other configurations
├── /Tests                # Unit and integration tests
│
├── Program.cs            # Entry point and service configuration
├── Startup.cs            # (Optional in .NET 6+) Configuration for middleware and services
└── appsettings.json      # Application configuration
```

---

## 2. Dependency Injection (DI)
Use dependency injection to decouple components and improve testability.

- Register Services in `Program.cs` or `Startup.cs`:
  ```csharp
  builder.Services.AddScoped<IMyService, MyService>();
  builder.Services.AddTransient<IRepository, Repository>();
  builder.Services.AddSingleton<ICacheService, CacheService>();
  ```

- Avoid Service Locator Pattern: Inject dependencies via constructors instead of resolving them manually.

---

## 3. Controllers
Keep controllers thin and focused on handling HTTP requests.

- Best Practices:
  - Use `[ApiController]` attribute for API controllers.
  - Use action filters for cross-cutting concerns (e.g., logging, validation).
  - Return appropriate HTTP status codes and response types.
  - Avoid business logic in controllers; delegate to services.

  Example:
  ```csharp
  [ApiController]
  [Route("api/[controller]")]
  public class UsersController : ControllerBase
  {
      private readonly IUserService _userService;

      public UsersController(IUserService userService)
      {
          _userService = userService;
      }

      [HttpGet("{id}")]
      public async Task<IActionResult> GetUser(int id)
      {
          var user = await _userService.GetUserByIdAsync(id);
          if (user == null)
          {
              return NotFound();
          }
          return Ok(user);
      }
  }
  ```

---

## 4. Services
Encapsulate business logic in service classes.

- Best Practices:
  - Follow the Single Responsibility Principle (SRP).
  - Use interfaces for services to enable mocking in unit tests.
  - Avoid tight coupling between services.

  Example:
  ```csharp
  public interface IUserService
  {
      Task<UserDto> GetUserByIdAsync(int id);
  }

  public class UserService : IUserService
  {
      private readonly IUserRepository _userRepository;

      public UserService(IUserRepository userRepository)
      {
          _userRepository = userRepository;
      }

      public async Task<UserDto> GetUserByIdAsync(int id)
      {
          var user = await _userRepository.GetByIdAsync(id);
          return MapToDto(user);
      }

      private UserDto MapToDto(User user)
      {
          // Mapping logic
      }
  }
  ```

---

## 5. Repository Pattern (Optional)
Use the repository pattern to abstract data access logic.

- Best Practices:
  - Use a generic repository for common CRUD operations.
  - Avoid exposing database entities directly; use DTOs.

  Example:
  ```csharp
  public interface IRepository<T>
  {
      Task<T> GetByIdAsync(int id);
      Task<IEnumerable<T>> GetAllAsync();
      Task AddAsync(T entity);
      Task UpdateAsync(T entity);
      Task DeleteAsync(int id);
  }

  public class UserRepository : IRepository<User>
  {
      private readonly AppDbContext _context;

      public UserRepository(AppDbContext context)
      {
          _context = context;
      }

      public async Task<User> GetByIdAsync(int id)
      {
          return await _context.Users.FindAsync(id);
      }
  }
  ```

---

## 6. Validation
Use FluentValidation or Data Annotations for input validation.

- Example with FluentValidation:
  ```csharp
  public class UserValidator : AbstractValidator<UserDto>
  {
      public UserValidator()
      {
          RuleFor(x => x.Name).NotEmpty().MaximumLength(100);
          RuleFor(x => x.Email).NotEmpty().EmailAddress();
      }
  }
  ```

- Register Validators:
  ```csharp
  builder.Services.AddValidatorsFromAssemblyContaining<UserValidator>();
  ```

---

## 7. Logging
Use structured logging with Serilog or the built-in `ILogger`.

- Example:
  ```csharp
  public class UserService : IUserService
  {
      private readonly ILogger<UserService> _logger;

      public UserService(ILogger<UserService> logger)
      {
          _logger = logger;
      }

      public async Task<UserDto> GetUserByIdAsync(int id)
      {
          _logger.LogInformation("Fetching user with ID {UserId}", id);
          // Logic
      }
  }
  ```

---

## 8. Exception Handling
Use middleware for global exception handling.

- Custom Exception Middleware:
  ```csharp
  public class ExceptionMiddleware
  {
      private readonly RequestDelegate _next;

      public ExceptionMiddleware(RequestDelegate next)
      {
          _next = next;
      }

      public async Task InvokeAsync(HttpContext context)
      {
          try
          {
              await _next(context);
          }
          catch (Exception ex)
          {
              context.Response.StatusCode = StatusCodes.Status500InternalServerError;
              await context.Response.WriteAsJsonAsync(new { error = ex.Message });
          }
      }
  }
  ```

- Register Middleware:
  ```csharp
  app.UseMiddleware<ExceptionMiddleware>();
  ```

---

## 9. Testing
Write unit and integration tests for your application.

- Unit Tests (xUnit, NUnit, or MSTest):
  ```csharp
  public class UserServiceTests
  {
      [Fact]
      public async Task GetUserByIdAsync_ShouldReturnUser()
      {
          // Arrange
          var mockRepo = new Mock<IUserRepository>();
          mockRepo.Setup(repo => repo.GetByIdAsync(1)).ReturnsAsync(new User { Id = 1, Name = "John" });

          var service = new UserService(mockRepo.Object);

          // Act
          var result = await service.GetUserByIdAsync(1);

          // Assert
          Assert.NotNull(result);
          Assert.Equal("John", result.Name);
      }
  }
  ```

- Integration Tests:
  Use `WebApplicationFactory` to test your API endpoints.

---

## 10. Configuration
Use `appsettings.json` and environment-specific configurations.

- Example:
  ```json
  {
    "ConnectionStrings": {
      "DefaultConnection": "Server=.;Database=MyAppDb;Trusted_Connection=True;"
    },
    "Logging": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
  ```

- Access Configuration:
  ```csharp
  var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
  ```

---

## 11. Security
Implement security best practices.

- Use HTTPS:
  ```csharp
  builder.Services.AddHttpsRedirection(options => options.HttpsPort = 443);
  ```

- Enable CORS:
  ```csharp
  builder.Services.AddCors(options =>
  {
      options.AddPolicy("AllowAll", builder =>
      {
          builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader();
      });
  });
  ```

- Authentication and Authorization:
  Use JWT, Identity, or other authentication mechanisms.

---

## 12. Documentation
Use Swagger/OpenAPI for API documentation.

- Add Swagger:
  ```csharp
  builder.Services.AddEndpointsApiExplorer();
  builder.Services.AddSwaggerGen();
  ```

- Enable Swagger UI:
  ```csharp
  app.UseSwagger();
  app.UseSwaggerUI();
  ```

