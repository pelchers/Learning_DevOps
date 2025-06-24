# 🛠️ Development Environment Relationships: Your Local DevOps Foundation

## 📖 What This File Does
This guide explains how your local development environment tools work together to create a productive DevOps workflow. You'll understand how IDEs, terminals, package managers, and development servers connect to form your daily development experience.

## 🎯 Learning Objectives
- Understand how IDEs, terminals, and package managers relate to each other
- See how local development tools connect to Git/GitHub workflows
- Learn the relationship between development and production environments
- Understand how Node.js, npm, and package management fit together
- See how development servers relate to production deployment

## 📋 Prerequisites
- Understanding of Git/GitHub relationships (see `00-Git_GitHub_Foundation.md`)
- Basic familiarity with text editors and command-line concepts
- General awareness of web development (HTML, CSS, JavaScript)

---

## 🏗️ **The Development Environment Ecosystem**

### **🔄 How Local Tools Work Together**

```mermaid
flowchart TD
    subgraph "Code Creation Layer"
        A["VS Code<br/>Code Editor"] --> B["File System<br/>Project files"]
        C["Terminal<br/>Command interface"] --> B
        D["Browser<br/>Testing interface"] --> B
    end
    
    subgraph "Version Control Layer"
        B --> E["Git<br/>Local repository"]
        E --> F["GitHub<br/>Remote collaboration"]
    end
    
    subgraph "Package Management Layer"
        G["npm/yarn<br/>Package manager"] --> H["node_modules<br/>Dependencies"]
        I["package.json<br/>Project config"] --> G
        H --> B
    end
    
    subgraph "Development Server Layer"
        J["Node.js<br/>JavaScript runtime"] --> K["Development Server<br/>Local testing"]
        L["Hot Reload<br/>Live updates"] --> K
        K --> D
    end
    
    subgraph "Integration Layer"
        F --> M["CI/CD<br/>Automated testing"]
        M --> N["Production<br/>Deployed application"]
    end
    
    style A fill:#e8f5e8
    style E fill:#e8f5e8
    style F fill:#e3f2fd
    style J fill:#fff3e0
    style M fill:#f3e5f5
    style N fill:#ffebee
```

### **💡 Key Insight: Development ↔ Production Relationship**

> **📝 Quick Context for New Devs:**  
> Your laptop is like a small-scale version of a real website's infrastructure. When you run `npm start` and see your app on localhost:3000, you're essentially running the same type of server that powers Netflix or Twitter - just smaller and local to your machine.

Your local development environment is a **miniature version** of production:

| Local Development | Production Equivalent | Purpose |
|-------------------|----------------------|---------|
| **VS Code** | Code deployment | Writing and editing code |
| **Development Server** | Production server | Running your application |
| **localhost:3000** | your-domain.com | Accessing your application |
| **npm install** | Container/server setup | Installing dependencies |
| **Git commits** | Production deployments | Publishing changes |

> **🚀 Why This Matters:**  
> This isn't just an analogy - it's literally the same Node.js runtime, the same npm packages, and often the same code! The main differences are scale (production handles millions of users vs just you) and infrastructure (production has load balancers, databases, etc.).

---

## 🎨 **IDE and Code Editor Relationships**

### **🔄 VS Code as the Central Hub**

```mermaid
flowchart LR
    subgraph "VS Code Integrations"
        A["Code Editor<br/>Text editing"] --> B["Git Integration<br/>Source control"]
        C["Terminal Integration<br/>Built-in command line"] --> D["Extension Ecosystem<br/>Tools and languages"]
        E["Debugger<br/>Code troubleshooting"] --> F["Live Share<br/>Team collaboration"]
    end
    
    subgraph "External Connections"
        B --> G["GitHub<br/>Remote repositories"]
        C --> H["npm Commands<br/>Package management"]
        D --> I["Language Servers<br/>Code intelligence"]
        F --> J["Team Members<br/>Real-time collaboration"]
    end
    
    subgraph "File System"
        K["Project Folder"] --> A
        K --> B
        K --> C
        K --> L["node_modules<br/>Dependencies"]
    end
    
    style A fill:#e8f5e8
    style B fill:#e8f5e8
    style G fill:#e3f2fd
    style H fill:#fff3e0
    style I fill:#fff3e0
```

### **🎯 Why VS Code Dominates Development**

**Integration Benefits:**
- **Git built-in**: See changes, commit, push without leaving the editor
- **Terminal embedded**: Run commands without switching windows
- **Extension ecosystem**: One tool for all languages and frameworks
- **Live debugging**: Set breakpoints and inspect code execution
- **Team collaboration**: Share coding sessions in real-time

### **🔧 Alternative Editor Relationships**

```mermaid
flowchart TD
    subgraph "Editor Options"
        A["VS Code<br/>Microsoft"] --> B["Full-featured<br/>Beginner-friendly"]
        C["WebStorm<br/>JetBrains"] --> D["Professional<br/>Advanced features"]
        E["Vim/Neovim<br/>Terminal-based"] --> F["Lightweight<br/>Keyboard-focused"]
        G["Sublime Text<br/>Fast editor"] --> H["Speed<br/>Minimalist"]
    end
    
    subgraph "Common Integrations"
        I["Git Integration"] --> A
        I --> C
        I --> E
        I --> G
        
        J["Terminal Access"] --> A
        J --> C
        J --> E
        
        K["Language Support"] --> A
        K --> C
        K --> E
        K --> G
    end
    
    style A fill:#4caf50
    style I fill:#e3f2fd
    style J fill:#fff3e0
    style K fill:#f3e5f5
```

---

## 💻 **Terminal and Command-Line Relationships**

### **🔄 Terminal as the Universal Interface**

```mermaid
flowchart LR
    subgraph "Terminal Functions"
        A["Command Execution<br/>Run programs"] --> B["File Navigation<br/>Move between folders"]
        C["Git Operations<br/>Version control"] --> D["Package Management<br/>Install dependencies"]
        E["Server Control<br/>Start/stop applications"] --> F["System Operations<br/>Environment setup"]
    end
    
    subgraph "Integration Points"
        B --> G["VS Code<br/>Integrated terminal"]
        C --> H["GitHub<br/>Remote operations"]
        D --> I["npm/yarn<br/>Package managers"]
        E --> J["Node.js<br/>JavaScript runtime"]
        F --> K["Operating System<br/>System commands"]
    end
    
    subgraph "Workflow Examples"
        L["Development Cycle"] --> C
        L --> D
        L --> E
        M["Deployment Process"] --> C
        M --> F
        M --> N["CI/CD<br/>Automation"]
    end
    
    style A fill:#e8f5e8
    style C fill:#e8f5e8
    style H fill:#e3f2fd
    style I fill:#fff3e0
    style J fill:#fff3e0
    style N fill:#f3e5f5
```

### **💡 Terminal Skills for DevOps**

> **📝 Quick Context for New Devs:**  
> Don't let the terminal intimidate you! These commands are like keyboard shortcuts - they seem scary at first, but they're actually faster than clicking through menus once you learn them. You'll use these same commands whether you're working on a small project or managing servers for millions of users.

**Essential Command Categories:**
```bash
# File and directory operations
ls, cd, mkdir, rm, cp, mv

# Git operations  
git add, git commit, git push, git pull

# Package management
npm install, npm start, npm run build

# Server operations
node server.js, npm run dev, kill process

# System operations
ps, top, df, which, whereis
```

> **🚀 Pro Tip:**  
> Start with just 5 commands: `ls` (list files), `cd` (change directory), `git status`, `npm install`, and `npm start`. These five will handle 80% of your daily work. Add more commands as you need them - don't try to memorize everything at once!

### **🎯 Terminal Relationship to Other Tools**

| Terminal Command | Related Tool | What It Does |
|------------------|--------------|--------------|
| `git status` | VS Code Git panel | Shows file changes |
| `npm start` | Development server | Starts local application |
| `code .` | VS Code | Opens current folder in editor |
| `node --version` | Node.js runtime | Checks JavaScript environment |
| `npm install` | package.json | Installs project dependencies |

---

## 📦 **Package Management Relationships**

### **🔄 npm/Node.js Ecosystem**

```mermaid
flowchart TD
    subgraph "Package Management Core"
        A["package.json<br/>Project manifest"] --> B["npm install<br/>Dependency installation"]
        B --> C["node_modules<br/>Installed packages"]
        C --> D["Application Code<br/>Your project"]
    end
    
    subgraph "Development Workflow"
        E["npm start<br/>Development server"] --> F["localhost:3000<br/>Local testing"]
        G["npm run build<br/>Production build"] --> H["dist/ folder<br/>Optimized code"]
        I["npm test<br/>Quality assurance"] --> J["Test Results<br/>Code validation"]
    end
    
    subgraph "Deployment Pipeline"
        H --> K["Docker<br/>Containerization"]
        K --> L["AWS/Cloud<br/>Production hosting"]
        L --> M["Users<br/>Live application"]
    end
    
    subgraph "Version Control"
        A --> N["Git Repository<br/>Project history"]
        N --> O["GitHub<br/>Team collaboration"]
        O --> P["CI/CD<br/>Automated deployment"]
        P --> K
    end
    
    style A fill:#e8f5e8
    style B fill:#fff3e0
    style N fill:#e8f5e8
    style O fill:#e3f2fd
    style K fill:#f3e5f5
    style L fill:#ffebee
```

### **📋 package.json: The Project Relationship Hub**

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "build": "webpack --mode production",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0",
    "react": "^18.2.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.15",
    "jest": "^29.0.0"
  }
}
```

**Relationship Breakdown:**
- **`scripts`**: Commands that connect to development tools
- **`dependencies`**: Libraries your application needs in production
- **`devDependencies`**: Tools for development and building
- **`name`/`version`**: Identity for package registries and deployment

### **🎯 Development vs Production Dependencies**

> **📝 Quick Context for New Devs:**  
> Think of dependencies like tools in a toolbox. Development dependencies (devDependencies) are like your drafting tools - you use them while building, but you don't ship them with the final product. Production dependencies are like the actual screws and bolts - they're part of the final product that users interact with.

```mermaid
flowchart LR
    subgraph "Development Environment"
        A["devDependencies<br/>Development tools"] --> B["nodemon<br/>Auto-restart server"]
        A --> C["webpack<br/>Code bundling"]
        A --> D["jest<br/>Testing framework"]
    end
    
    subgraph "Production Environment"
        E["dependencies<br/>Runtime libraries"] --> F["express<br/>Web server"]
        E --> G["react<br/>UI framework"]
        E --> H["database drivers<br/>Data access"]
    end
    
    subgraph "Build Process"
        B --> I["Development Server<br/>Hot reload"]
        C --> J["Production Bundle<br/>Optimized code"]
        D --> K["Test Results<br/>Quality gates"]
    end
    
    subgraph "Deployment"
        F --> L["Production Server<br/>Live application"]
        G --> L
        H --> L
        J --> L
    end
    
    style A fill:#e8f5e8
    style E fill:#fff3e0
    style I fill:#e8f5e8
    style L fill:#ffebee
```

> **💰 Why This Matters for Performance:**  
> Keeping development tools out of production makes your application smaller and faster. A React app might need 50MB of dev tools during development but only ship 2MB to users. This means faster load times and lower bandwidth costs.

---

## 🌐 **Development Server Relationships**

### **🔄 Local Development Server Ecosystem**

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant VS as VS Code
    participant Term as Terminal
    participant Server as Dev Server
    participant Browser as Browser
    participant Files as File System
    
    Dev->>VS: Edit code files
    VS->>Files: Save changes
    Files->>Server: Detect file changes
    Server->>Browser: Hot reload update
    Browser->>Dev: Show updated application
    
    Dev->>Term: npm start
    Term->>Server: Start development server
    Server->>Browser: Open localhost:3000
    
    Dev->>VS: Make more changes
    VS->>Files: Auto-save enabled
    Files->>Server: Watch for changes
    Server->>Browser: Live updates
    
    Note over Dev, Browser: Continuous development cycle
    Note over Server: Hot reload enables instant feedback
```

### **🎯 Development Server Technologies**

| Technology | What It Provides | Relationship to Production |
|------------|------------------|----------------------------|
| **Webpack Dev Server** | Hot module replacement | Simulates production bundling |
| **Vite** | Fast rebuilds and HMR | Modern build tool alternative |
| **Node.js + Express** | Backend API development | Direct production equivalent |
| **Create React App** | Full-stack development | Production build pipeline |
| **Next.js** | Full-stack React development | Server-side rendering + API |

### **💡 Development vs Production Server Relationship**

```mermaid
flowchart LR
    subgraph "Development (Your Computer)"
        A["Code Changes"] --> B["Hot Reload<br/>Instant updates"]
        B --> C["localhost:3000<br/>Local testing"]
        C --> D["Development Tools<br/>Debugging, logs"]
    end
    
    subgraph "Production (Cloud Server)"
        E["Deployed Code"] --> F["Optimized Build<br/>Performance focused"]
        F --> G["your-domain.com<br/>Public access"]
        G --> H["Production Tools<br/>Monitoring, scaling"]
    end
    
    subgraph "Bridge Technologies"
        I["CI/CD Pipeline"] --> E
        J["Docker Containers"] --> F
        K["Environment Variables"] --> G
        L["Logging Systems"] --> H
    end
    
    A --> I
    B --> J
    C --> K
    D --> L
    
    style A fill:#e8f5e8
    style C fill:#e8f5e8
    style E fill:#fff3e0
    style G fill:#ffebee
    style I fill:#f3e5f5
```

---

## 🔗 **Integration with Git/GitHub Workflows**

### **🔄 Complete Development Cycle**

```mermaid
flowchart TD
    subgraph "Local Development"
        A["VS Code<br/>Edit code"] --> B["Git<br/>Track changes"]
        C["Terminal<br/>Run commands"] --> D["Dev Server<br/>Test locally"]
        E["Browser<br/>View application"] --> F["Debug<br/>Fix issues"]
    end
    
    subgraph "Version Control"
        B --> G["git add<br/>Stage changes"]
        G --> H["git commit<br/>Save locally"]
        H --> I["git push<br/>Share with team"]
    end
    
    subgraph "Remote Collaboration"
        I --> J["GitHub<br/>Central repository"]
        J --> K["Pull Request<br/>Code review"]
        K --> L["Team Review<br/>Collaboration"]
    end
    
    subgraph "Automation"
        L --> M["Merge to main<br/>Approved changes"]
        M --> N["CI/CD Pipeline<br/>Automated build"]
        N --> O["Production Deploy<br/>Live application"]
    end
    
    subgraph "Feedback Loop"
        O --> P["Monitoring<br/>Application health"]
        P --> Q["Issue Detection<br/>Problems found"]
        Q --> A
    end
    
    style A fill:#e8f5e8
    style B fill:#e8f5e8
    style I fill:#e3f2fd
    style J fill:#e3f2fd
    style N fill:#f3e5f5
    style O fill:#ffebee
```

### **🎯 Daily Development Workflow Integration**

```bash
# Morning: Start development
code .                          # Open VS Code
git pull origin main           # Get team updates
npm install                    # Update dependencies
npm start                      # Start development server

# During development: Continuous cycle
# 1. Edit code in VS Code
# 2. See changes in browser (localhost:3000)
# 3. Test functionality
# 4. Debug issues

# End of day: Share progress
git add .                      # Stage changes
git commit -m "feat: add user login"  # Save locally
git push origin feature-branch # Share with team

# Deployment: Automated process
# 1. Create pull request on GitHub
# 2. Team reviews code
# 3. Merge triggers CI/CD
# 4. Application deploys to production
```

---

## 🛠️ **Tool Relationships by Development Type**

### **🌐 Frontend Development Stack**

```mermaid
flowchart LR
    subgraph "Frontend Tools"
        A["React/Vue/Angular<br/>UI Framework"] --> B["Webpack/Vite<br/>Build tool"]
        C["CSS/Sass<br/>Styling"] --> B
        D["TypeScript<br/>Type safety"] --> B
        E["ESLint/Prettier<br/>Code quality"] --> B
    end
    
    subgraph "Development Environment"
        F["VS Code<br/>Editor"] --> A
        F --> C
        F --> D
        F --> E
        G["Browser DevTools<br/>Debugging"] --> A
        H["npm/yarn<br/>Package manager"] --> A
    end
    
    subgraph "Integration"
        B --> I["Development Server<br/>Hot reload"]
        B --> J["Production Build<br/>Optimized code"]
        I --> K["localhost:3000<br/>Local testing"]
        J --> L["CDN/Hosting<br/>Production deployment"]
    end
    
    style F fill:#e8f5e8
    style A fill:#fff3e0
    style I fill:#e8f5e8
    style L fill:#ffebee
```

### **🔧 Backend Development Stack**

```mermaid
flowchart LR
    subgraph "Backend Tools"
        A["Node.js<br/>JavaScript runtime"] --> B["Express/Fastify<br/>Web framework"]
        C["Database<br/>Data storage"] --> B
        D["API Design<br/>REST/GraphQL"] --> B
        E["Authentication<br/>User management"] --> B
    end
    
    subgraph "Development Environment"
        F["VS Code<br/>Editor"] --> A
        F --> C
        F --> D
        F --> E
        G["Postman/Insomnia<br/>API testing"] --> B
        H["Database Tools<br/>Data management"] --> C
    end
    
    subgraph "Integration"
        B --> I["Development Server<br/>nodemon/pm2"]
        B --> J["Production Server<br/>Docker/AWS"]
        I --> K["localhost:3001<br/>API endpoint"]
        J --> L["your-api.com<br/>Production API"]
    end
    
    style F fill:#e8f5e8
    style A fill:#fff3e0
    style I fill:#e8f5e8
    style L fill:#ffebee
```

### **🔄 Full-Stack Development Integration**

```mermaid
flowchart TD
    subgraph "Frontend (localhost:3000)"
        A["React App<br/>User interface"] --> B["API Calls<br/>Data requests"]
    end
    
    subgraph "Backend (localhost:3001)"
        C["Express Server<br/>API endpoints"] --> D["Database<br/>Data storage"]
    end
    
    subgraph "Development Tools"
        E["VS Code<br/>Code editor"] --> A
        E --> C
        F["Browser<br/>Frontend testing"] --> A
        G["Postman<br/>API testing"] --> C
        H["Database Client<br/>Data management"] --> D
    end
    
    subgraph "Production Deployment"
        I["Frontend Build<br/>Static files"] --> J["CDN/Netlify<br/>Frontend hosting"]
        K["Backend Deploy<br/>API server"] --> L["AWS/Heroku<br/>Backend hosting"]
        M["Database Deploy<br/>Production data"] --> N["AWS RDS<br/>Managed database"]
    end
    
    B --> C
    A --> I
    C --> K
    D --> M
    
    style E fill:#e8f5e8
    style A fill:#fff3e0
    style C fill:#fff3e0
    style J fill:#ffebee
    style L fill:#ffebee
    style N fill:#ffebee
```

---

## 🎯 **Environment Configuration Relationships**

### **🔧 Environment Variables and Configuration**

```mermaid
flowchart LR
    subgraph "Configuration Files"
        A[".env<br/>Environment variables"] --> B["config.js<br/>Application config"]
        C["package.json<br/>Project metadata"] --> D["Scripts<br/>Command definitions"]
    end
    
    subgraph "Environment Types"
        E["Development<br/>Local machine"] --> F["DATABASE_URL=localhost<br/>Local database"]
        G["Staging<br/>Testing server"] --> H["DATABASE_URL=staging.db<br/>Test database"]
        I["Production<br/>Live server"] --> J["DATABASE_URL=prod.db<br/>Production database"]
    end
    
    subgraph "Security"
        K[".gitignore<br/>Exclude secrets"] --> A
        L["GitHub Secrets<br/>Secure storage"] --> G
        L --> I
    end
    
    A --> E
    A --> G
    A --> I
    
    style A fill:#e8f5e8
    style E fill:#e8f5e8
    style G fill:#fff3e0
    style I fill:#ffebee
    style K fill:#e8f5e8
```

### **💡 Configuration Best Practices**

**Development Environment Setup:**
```bash
# .env file (local development)
DATABASE_URL=postgresql://localhost:5432/myapp_dev
API_KEY=dev_api_key_12345
PORT=3000
NODE_ENV=development

# package.json scripts
{
  "scripts": {
    "dev": "NODE_ENV=development nodemon server.js",
    "start": "NODE_ENV=production node server.js",
    "test": "NODE_ENV=test jest"
  }
}
```

**Environment Separation Benefits:**
- **Development**: Fast feedback, detailed logging, hot reload
- **Staging**: Production-like testing, integration validation
- **Production**: Performance optimized, monitoring enabled, security hardened

---

## 🚨 **Common Development Environment Issues**

### **❌ Problem: "It works on my machine"**

```mermaid
flowchart LR
    subgraph "Developer A Machine"
        A["Node.js 18.0<br/>Latest version"] --> B["Application Works<br/>Perfect functionality"]
    end
    
    subgraph "Developer B Machine"
        C["Node.js 16.0<br/>Older version"] --> D["Application Breaks<br/>Compatibility issues"]
    end
    
    subgraph "Solutions"
        E[".nvmrc<br/>Node version file"] --> F["Team uses same Node version"]
        G["Docker<br/>Containerization"] --> H["Identical environments"]
        I["package-lock.json<br/>Exact dependency versions"] --> J["Consistent dependencies"]
    end
    
    B --> E
    D --> G
    B --> I
    D --> I
    
    style A fill:#e8f5e8
    style C fill:#ffebee
    style E fill:#fff3e0
    style G fill:#f3e5f5
    style I fill:#e3f2fd
```

### **✅ Solution: Environment Standardization**

**Tools for Consistency:**
- **`.nvmrc`**: Specify Node.js version for the project
- **`package-lock.json`**: Lock exact dependency versions
- **Docker**: Containerize development environment
- **VS Code settings**: Share editor configuration
- **ESLint/Prettier**: Enforce code formatting standards

---

## 🔄 **Next Steps in Your Learning Journey**

### **🎯 Development Environment Mastery**

1. **Master your IDE**: Learn VS Code shortcuts and extensions
2. **Terminal proficiency**: Practice Git and npm commands daily
3. **Package management**: Understand dependencies vs devDependencies
4. **Development servers**: Set up hot reload and debugging
5. **Environment configuration**: Manage multiple environments properly

### **🔗 Related Files to Read Next**

- **`02-Linux_System_Relationships.md`**: How your development environment runs on the operating system
- **`04-Containerization_Relationships.md`**: How Docker standardizes development environments
- **`06-CI_CD_Pipeline_Relationships.md`**: How local development connects to automated deployment

### **💡 Key Relationship Concepts**

- **Local mirrors production**: Your development environment should simulate production
- **Git connects everything**: Version control integrates all development tools
- **Package managers handle dependencies**: npm/yarn manage your project's requirements
- **Development servers enable rapid feedback**: Hot reload accelerates development cycles
- **Environment configuration ensures consistency**: Same code, different environments

---

## 🔧 **Configuration Notes**

- **Tool Integration**: Choose tools that work well together (VS Code + Terminal + Git)
- **Environment Parity**: Keep development and production as similar as possible
- **Team Consistency**: Standardize tools and configurations across the team
- **Security**: Never commit secrets, use environment variables and .gitignore

---

## 📚 **Terminology**

### **Development Environment**
- **IDE (Integrated Development Environment)**: Complete development platform with editor, debugger, and tools
- **Code Editor**: Text editor optimized for writing code
- **Extension**: Add-on that provides additional functionality to an editor
- **Workspace**: Project folder and its configuration in an IDE
- **IntelliSense**: Code completion and suggestions based on context
- **Syntax Highlighting**: Color-coding of code elements for readability
- **Linting**: Automated code analysis for errors and style issues
- **Debugging**: Process of finding and fixing code errors

### **Terminal and Command Line**
- **Terminal**: Text-based interface for running commands
- **Shell**: Program that interprets and executes commands (bash, zsh, PowerShell)
- **Command Line Interface (CLI)**: Text-based user interface
- **Path**: Location of files or programs in the file system
- **Environment Variables**: System-wide settings accessible to programs
- **Script**: File containing commands to be executed by the shell
- **Alias**: Custom shortcut for frequently used commands
- **Tab Completion**: Auto-complete feature for commands and file names

### **Package Management**
- **Package Manager**: Tool for installing and managing software dependencies
- **Dependency**: External library or tool required by your project
- **package.json**: Configuration file defining Node.js project metadata and dependencies
- **node_modules**: Folder containing installed npm packages
- **npm (Node Package Manager)**: Default package manager for Node.js
- **yarn**: Alternative package manager for JavaScript projects
- **Semantic Versioning**: Version numbering system (major.minor.patch)
- **Lock File**: File ensuring consistent dependency versions across environments

### **Development Servers**
- **Development Server**: Local server for testing applications during development
- **Hot Reload/HMR (Hot Module Replacement)**: Automatic browser refresh when code changes
- **Live Reload**: Browser refresh when files are saved
- **Port**: Network endpoint where services listen for connections
- **localhost**: Loopback address (127.0.0.1) referring to your local machine
- **Proxy**: Intermediate server that forwards requests to another server
- **Build Tool**: Software that compiles and optimizes code for production
- **Bundler**: Tool that combines multiple files into optimized bundles

### **Environment Configuration**
- **Environment Variables**: Configuration values stored outside the code
- **.env File**: File containing environment-specific configuration
- **Configuration Management**: Practice of handling settings across different environments
- **Development vs Production**: Different environment types with specific configurations
- **Environment Parity**: Keeping development and production environments similar
- **.gitignore**: File specifying which files Git should ignore
- **dotfiles**: Configuration files starting with a dot (hidden files)
- **PATH Variable**: System variable listing directories containing executable programs

### **File System and Project Structure**
- **Working Directory**: Current folder where commands are executed
- **Root Directory**: Top-level directory of a file system or project
- **Relative Path**: File path relative to current directory
- **Absolute Path**: Complete file path from root directory
- **Symlink (Symbolic Link)**: Reference pointing to another file or directory
- **Project Structure**: Organization of files and folders in a codebase
- **Source Control**: Managing code changes and versions
- **Backup**: Copy of files for disaster recovery

---

📄 **File Path:** `/Tech_Relationships/01-Development_Environment_Relationships.md` 