# 📦 Node.js & NPM Commands Reference

## 📋 Command Directory

| Command | Description |
|---------|-------------|
| [node --version](#node---version) | Check Node.js version |
| [npm --version](#npm---version) | Check NPM version |
| [npm init](#npm-init) | Initialize new Node.js project |
| [npm install](#npm-install) | Install packages |
| [npm uninstall](#npm-uninstall) | Remove packages |
| [npm update](#npm-update) | Update packages |
| [npm list](#npm-list) | List installed packages |
| [npm run](#npm-run) | Execute package scripts |
| [npm start](#npm-start) | Start application |
| [npm test](#npm-test) | Run tests |
| [npm audit](#npm-audit) | Check for security vulnerabilities |
| [npm publish](#npm-publish) | Publish package to registry |
| [npm link](#npm-link) | Create symlinks for local development |
| [npx](#npx) | Execute packages without installing |
| [npm config](#npm-config) | Manage NPM configuration |
| [npm cache](#npm-cache) | Manage NPM cache |

---

## Version Management

### node --version

**What it does:** Displays the currently installed Node.js version

**Syntax:**
```bash
node --version
node -v
```

**Example Usage:**
```bash
# Check Node.js version
node --version
# Output: v18.17.0

# Short version
node -v
```

**Important Notes:**
- Use to verify Node.js installation
- Different projects may require different Node versions
- Consider using Node Version Manager (nvm) for version switching

---

### npm --version

**What it does:** Shows the currently installed NPM version

**Syntax:**
```bash
npm --version
npm -v
```

**Example Usage:**
```bash
# Check NPM version
npm --version
# Output: 9.6.7

# Check all versions (Node, NPM, dependencies)
npm version
```

**Important Notes:**
- NPM version is tied to Node.js installation
- Can be updated independently of Node.js
- Use `npm install -g npm@latest` to update NPM

---

## Project Initialization

### npm init

**What it does:** Creates a new package.json file for your project

**Syntax:**
```bash
npm init [options]
```

**Example Usage:**
```bash
# Interactive initialization
npm init

# Skip questions with defaults
npm init -y

# Use specific template
npm init react-app my-app

# Initialize with custom values
npm init --name="my-project" --version="1.0.0"
```

**Important Notes:**
- Creates package.json with project metadata
- Use `-y` flag to accept all defaults quickly
- package.json is required for NPM package management

---

## Package Installation

### npm install

**What it does:** Installs packages from NPM registry

**Syntax:**
```bash
npm install [package-name] [options]
```

**Example Usage:**
```bash
# Install all dependencies from package.json
npm install

# Install specific package
npm install express

# Install as development dependency
npm install --save-dev nodemon

# Install globally
npm install -g pm2

# Install specific version
npm install lodash@4.17.21

# Install from GitHub
npm install https://github.com/user/repo.git

# Install and save to package.json
npm install --save axios

# Install without saving to package.json
npm install --no-save temporary-package
```

**Important Notes:**
- Creates `node_modules` folder with installed packages
- `--save` is default behavior (adds to dependencies)
- `--save-dev` adds to devDependencies
- Global packages (`-g`) are available system-wide

---

### npm uninstall

**What it does:** Removes packages from your project

**Syntax:**
```bash
npm uninstall [package-name] [options]
```

**Example Usage:**
```bash
# Uninstall package and remove from package.json
npm uninstall express

# Uninstall dev dependency
npm uninstall --save-dev nodemon

# Uninstall global package
npm uninstall -g pm2

# Uninstall without updating package.json
npm uninstall --no-save package-name
```

**Important Notes:**
- Removes package from node_modules and package.json
- Use same flags as when installing (--save-dev, -g)
- Clean up unused dependencies regularly

---

### npm update

**What it does:** Updates packages to their latest versions

**Syntax:**
```bash
npm update [package-name]
```

**Example Usage:**
```bash
# Update all packages
npm update

# Update specific package
npm update express

# Update global packages
npm update -g

# Check for outdated packages
npm outdated

# Update to latest (including major versions)
npm install package-name@latest
```

**Important Notes:**
- Respects version ranges in package.json
- Won't update across major versions by default
- Use `npm outdated` to see available updates

---

## Package Information

### npm list

**What it does:** Lists installed packages and their dependencies

**Syntax:**
```bash
npm list [options]
```

**Example Usage:**
```bash
# List all installed packages
npm list

# List only top-level packages
npm list --depth=0

# List global packages
npm list -g --depth=0

# List specific package
npm list express

# List in JSON format
npm list --json

# List production dependencies only
npm list --prod
```

**Important Notes:**
- Shows dependency tree structure
- Use `--depth=0` to avoid overwhelming output
- Helps identify package versions and dependencies

---

## Script Execution

### npm run

**What it does:** Executes scripts defined in package.json

**Syntax:**
```bash
npm run [script-name]
```

**Example Usage:**
```bash
# Run custom script
npm run build

# Run development server
npm run dev

# List all available scripts
npm run

# Run script with arguments
npm run test -- --coverage

# Run multiple scripts
npm run build && npm run deploy
```

**Important Notes:**
- Scripts are defined in package.json "scripts" section
- Can chain commands with `&&` or `||`
- Use `--` to pass arguments to the script

---

### npm start

**What it does:** Runs the start script defined in package.json

**Syntax:**
```bash
npm start
```

**Example Usage:**
```bash
# Start the application
npm start

# Equivalent to
npm run start
```

**Important Notes:**
- Shorthand for `npm run start`
- Commonly used to start web servers
- Default behavior runs `node server.js` if no start script defined

---

### npm test

**What it does:** Runs the test script defined in package.json

**Syntax:**
```bash
npm test
```

**Example Usage:**
```bash
# Run tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run specific test file
npm test -- user.test.js
```

**Important Notes:**
- Shorthand for `npm run test`
- Often configured to run testing frameworks like Jest
- Use `--` to pass arguments to test runner

---

## Security and Maintenance

### npm audit

**What it does:** Scans project for security vulnerabilities

**Syntax:**
```bash
npm audit [options]
```

**Example Usage:**
```bash
# Check for vulnerabilities
npm audit

# Get detailed report
npm audit --audit-level=low

# Automatically fix issues
npm audit fix

# Force fixes (potentially breaking)
npm audit fix --force

# Get JSON report
npm audit --json
```

**Important Notes:**
- Checks dependencies against known vulnerability database
- `npm audit fix` attempts automatic fixes
- Review changes before committing fixes

---

## Package Development

### npm publish

**What it does:** Publishes package to NPM registry

**Syntax:**
```bash
npm publish [options]
```

**Example Usage:**
```bash
# Publish package
npm publish

# Publish with tag
npm publish --tag beta

# Publish scoped package as public
npm publish --access public

# Dry run (test without publishing)
npm publish --dry-run
```

**Important Notes:**
- Requires NPM account and login (`npm login`)
- Version must be unique (increment in package.json)
- Use `.npmignore` to exclude files from package

---

### npm link

**What it does:** Creates symbolic links for local package development

**Syntax:**
```bash
npm link [package-name]
```

**Example Usage:**
```bash
# In package directory - create global link
npm link

# In project directory - link to local package
npm link my-local-package

# Unlink package
npm unlink my-local-package

# List linked packages
npm ls --link --global
```

**Important Notes:**
- Useful for testing local packages before publishing
- Creates symlinks in global node_modules
- Changes to linked package are immediately reflected

---

## Package Execution

### npx

**What it does:** Executes packages without installing them globally

**Syntax:**
```bash
npx [package-name] [arguments]
```

**Example Usage:**
```bash
# Run create-react-app without installing
npx create-react-app my-app

# Execute local package binary
npx nodemon server.js

# Run specific version
npx cowsay@1.4.0 "Hello World"

# Use with arguments
npx eslint --init

# Force download latest version
npx --ignore-existing create-react-app my-app
```

**Important Notes:**
- Downloads and runs packages temporarily
- Checks local node_modules first, then downloads if needed
- Great for one-time tools and scaffolding

---

## Configuration

### npm config

**What it does:** Manages NPM configuration settings

**Syntax:**
```bash
npm config [command] [key] [value]
```

**Example Usage:**
```bash
# List all config settings
npm config list

# Get specific config value
npm config get registry

# Set config value
npm config set registry https://registry.npmjs.org/

# Delete config setting
npm config delete proxy

# Edit config file
npm config edit

# Set default author info
npm config set init-author-name "John Doe"
npm config set init-author-email "john@example.com"
```

**Important Notes:**
- Settings stored in .npmrc files
- Can be set globally or per-project
- Common settings: registry, proxy, author info

---

### npm cache

**What it does:** Manages NPM's local cache

**Syntax:**
```bash
npm cache [command]
```

**Example Usage:**
```bash
# Verify cache integrity
npm cache verify

# Clear entire cache
npm cache clean --force

# Show cache location
npm config get cache

# Add package to cache
npm cache add express@4.18.0
```

**Important Notes:**
- Cache speeds up package installation
- Located in system's cache directory
- Clean cache if experiencing install issues

---

**💡 Pro Tips:**
- Use `npm ci` instead of `npm install` in CI/CD pipelines for faster, reliable installs
- Create `.nvmrc` file with Node version for team consistency
- Use `npm shrinkwrap` or `package-lock.json` for reproducible installs 