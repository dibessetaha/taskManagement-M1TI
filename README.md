# TaskControl - Git Setup & Push Order Guide

## 📋 Quick Setup

### Step 1: Initialize Git Repository
```bash
cd TaskControl
git init
git branch -M main
```

---

### Step 2: Create .gitignore

Create a file named `.gitignore` in the root folder with this content:
```gitignore
## Visual Studio
.vs/
.vscode/

## Build results
bin/
obj/
[Dd]ebug/
[Rr]elease/

## Database
*.db
*.db-shm
*.db-wal

## User files
*.user
*.suo

## Environment
appsettings.Development.json
.env
```

---

### Step 3: Connect to GitLab
```bash
git remote add origin YOUR_GITLAB_URL
```

Replace `YOUR_GITLAB_URL` with your actual GitLab repository URL.

**Example:**
```bash
git remote add origin https://gitlab.com/username/taskcontrol.git
```

---

## 🎯 Push Order (Logical Structure)

### COMMIT 1: Infrastructure Files
```bash
git add .gitignore
git add README.md
git add TaskControl.csproj
git add TaskControl.sln
git commit -m "Initial commit - Project infrastructure"
git push -u origin main
```

---

### COMMIT 2: Configuration Files
```bash
git add appsettings.json
git add Program.cs
git add Properties/
git commit -m "Add configuration files"
git push
```

---

### COMMIT 3: Base Enums (No Dependencies)
```bash
git add Models/Entities/UserRole.cs
git add Models/Entities/TaskPriority.cs
git add Models/Entities/TaskItemStatus.cs
git commit -m "Add enums (UserRole, TaskPriority, TaskItemStatus)"
git push
```

---

### COMMIT 4: Base Entity (User - No Dependencies)
```bash
git add Models/Entities/User.cs
git commit -m "Add User entity"
git push
```

---

### COMMIT 5: Team Entities (Depends on User)
```bash
git add Models/Entities/Team.cs
git add Models/Entities/TeamMember.cs
git commit -m "Add Team and TeamMember entities"
git push
```

---

### COMMIT 6: Project Entity (Depends on User & Team)
```bash
git add Models/Entities/Project.cs
git commit -m "Add Project entity"
git push
```

---

### COMMIT 7: TaskItem Entity (Depends on Project & User)
```bash
git add Models/Entities/TaskItem.cs
git commit -m "Add TaskItem entity"
git push
```

---

### COMMIT 8: Comment Entity (Depends on TaskItem & User)
```bash
git add Models/Entities/Comment.cs
git commit -m "Add Comment entity"
git push
```

---

### COMMIT 9: Database Context (Depends on All Entities)
```bash
git add Data/ApplicationDbContext.cs
git commit -m "Add ApplicationDbContext with entity configurations"
git push
```

---

### COMMIT 10: Migrations (Generated from DbContext)
```bash
git add Migrations/
git commit -m "Add database migrations"
git push
```

---

### COMMIT 11: ViewModels (No Dependencies)
```bash
git add Models/ViewModels/
git commit -m "Add ViewModels for all features"
git push
```

---

### COMMIT 12: Service Interfaces
```bash
git add Services/Interfaces/
git commit -m "Add service interfaces"
git push
```

---

### COMMIT 13: Service Implementations (Depends on Interfaces & DbContext)
```bash
git add Services/AuthService.cs
git add Services/ProjectService.cs
git add Services/TaskService.cs
git add Services/TeamService.cs
git commit -m "Add service implementations"
git push
```

---

### COMMIT 14: Helpers (Optional - No Dependencies)
```bash
git add Helpers/
git commit -m "Add helper classes"
git push
```

---

### COMMIT 15: Controllers (Depends on Services)
```bash
git add Controllers/AuthController.cs
git add Controllers/ProjectController.cs
git add Controllers/TaskController.cs
git add Controllers/TeamController.cs
git commit -m "Add controllers"
git push
```

---

### COMMIT 16: Shared Views (Layout & Partials)
```bash
git add Views/Shared/
git add Views/_ViewImports.cshtml
git add Views/_ViewStart.cshtml
git commit -m "Add shared views and layout"
git push
```

---

### COMMIT 17: Auth Views
```bash
git add Views/Auth/
git commit -m "Add authentication views"
git push
```

---

### COMMIT 18: Project Views
```bash
git add Views/Project/
git commit -m "Add project views"
git push
```

---
