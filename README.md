
/CleanASPCoreTemplate                # 🔹 Root folder (Solution Level)
│-- YourProjectName.sln           # 📝 Solution file
│-- .gitignore                    # 📌 Ignore unnecessary files
│-- README.md                     # 📖 Project documentation
│-- /src                          # 🔹 Source code folder
│   │-- /WebApi                   # 🌐 API project (or MVC)
│   │   │-- WebApi.csproj          # 📝 API project file
│   │   │-- /Controllers          # 📂 API controllers
│   │   │-- /Views                # 📂 (If MVC, add Views here)
│   │   │-- /wwwroot              # 📂 Static files (CSS, JS)
│   │   └── Program.cs             # 🚀 Main entry point
│   │-- /Application              # 🧠 Business logic layer (Use Cases)
│   │   │-- Application.csproj    # 📝 Application project file
│   │   │-- /Services             # 🛠️ Business logic
│   │   │-- /Interfaces           # 🔌 Interfaces for services
│   │   │-- /DTOs                 # 📦 Data Transfer Objects
│   │-- /Domain                   # 📜 Core domain layer
│   │   │-- Domain.csproj         # 📝 Domain project file
│   │   │-- /Entities             # 🎭 Business entities (models)
│   │   │-- /Enums                # 🔢 Enums
│   │   │-- /ValueObjects         # 🧩 Value objects
│   │-- /Infrastructure           # 🏗️ Infrastructure Layer (Persistence, External Services)
│   │   │-- Infrastructure.csproj # 📝 Infrastructure project file
│   │   │-- /Data                 # 🗄️ Database (EF Core, Repositories)
│   │   │-- /Logging              # 📜 Logging setup
│   │   │-- /Services             # 🔌 External API calls
│-- /tests                        # ✅ Unit and Integration Tests
│   │-- /WebApi.Tests             # 🔹 Tests for Web API
│   │-- /Application.Tests        # 🔹 Tests for Business Logic
│   │-- /Domain.Tests             # 🔹 Tests for Core Entities
│   │-- /Infrastructure.Tests     # 🔹 Tests for Database Layer
│-- /build                        # 🏗️ CI/CD Scripts (Optional)
│-- /docker                       # 🐳 Docker-related files (Optional)
│-- /docs                         # 📖 Documentation (Optional)
│-- /tools                        # 🛠️ Dev tools, scripts (Optional)
