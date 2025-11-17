TaskControl - Git Setup & Push Order Guide
📋 Quick Setup
Step 1: Initialize Git Repository
bashcd TaskControl
git init
git branch -M main

Step 2: Create .gitignore
Create a file named .gitignore in the root folder with this content:
gitignore## Visual Studio
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

Step 3: Connect to GitLab
bashgit remote add origin YOUR_GITLAB_URL
Replace YOUR_GITLAB_URL with your actual GitLab repository URL.
Example:
bashgit remote add origin https://gitlab.com/username/taskcontrol.git

🎯 Push Order (Logical Structure)
COMMIT 1: Infrastructure Files
bashgit add .gitignore
git add README.md
git add TaskControl.csproj
git add TaskControl.sln
git commit -m "Initial commit - Project infrastructure"
git push -u origin main

COMMIT 2: Configuration Files
bashgit add appsettings.json
git add Program.cs
git add Properties/
git commit -m "Add configuration files"
git push

COMMIT 3: Base Enums (No Dependencies)
bashgit add Models/Entities/UserRole.cs
git add Models/Entities/TaskPriority.cs
git add Models/Entities/TaskItemStatus.cs
git commit -m "Add enums (UserRole, TaskPriority, TaskItemStatus)"
git push

COMMIT 4: Base Entity (User - No Dependencies)
bashgit add Models/Entities/User.cs
git commit -m "Add User entity"
git push

COMMIT 5: Team Entities (Depends on User)
bashgit add Models/Entities/Team.cs
git add Models/Entities/TeamMember.cs
git commit -m "Add Team and TeamMember entities"
git push

COMMIT 6: Project Entity (Depends on User & Team)
bashgit add Models/Entities/Project.cs
git commit -m "Add Project entity"
git push

COMMIT 7: TaskItem Entity (Depends on Project & User)
bashgit add Models/Entities/TaskItem.cs
git commit -m "Add TaskItem entity"
git push

COMMIT 8: Comment Entity (Depends on TaskItem & User)
bashgit add Models/Entities/Comment.cs
git commit -m "Add Comment entity"
git push

COMMIT 9: Database Context (Depends on All Entities)
bashgit add Data/ApplicationDbContext.cs
git commit -m "Add ApplicationDbContext with entity configurations"
git push

COMMIT 10: Migrations (Generated from DbContext)
bashgit add Migrations/
git commit -m "Add database migrations"
git push

COMMIT 11: ViewModels (No Dependencies)
bashgit add Models/ViewModels/
git commit -m "Add ViewModels for all features"
git push

COMMIT 12: Service Interfaces
bashgit add Services/Interfaces/
git commit -m "Add service interfaces"
git push

COMMIT 13: Service Implementations (Depends on Interfaces & DbContext)
bashgit add Services/AuthService.cs
git add Services/ProjectService.cs
git add Services/TaskService.cs
git add Services/TeamService.cs
git commit -m "Add service implementations"
git push

COMMIT 14: Helpers (Optional - No Dependencies)
bashgit add Helpers/
git commit -m "Add helper classes"
git push

COMMIT 15: Controllers (Depends on Services)
bashgit add Controllers/AuthController.cs
git add Controllers/ProjectController.cs
git add Controllers/TaskController.cs
git add Controllers/TeamController.cs
git commit -m "Add controllers"
git push

COMMIT 16: Shared Views (Layout & Partials)
bashgit add Views/Shared/
git add Views/_ViewImports.cshtml
git add Views/_ViewStart.cshtml
git commit -m "Add shared views and layout"
git push

COMMIT 17: Auth Views
bashgit add Views/Auth/
git commit -m "Add authentication views"
git push

COMMIT 18: Project Views
bashgit add Views/Project/
git commit -m "Add project views"
git push

COMMIT 19: Task Views
bashgit add Views/Task/
git commit -m "Add task views"
git push

COMMIT 20: Team Views
bashgit add Views/Team/
git commit -m "Add team views"
git push

COMMIT 21: Static Files (wwwroot)
bashgit add wwwroot/
git commit -m "Add static files (CSS, JS, libraries)"
git push
