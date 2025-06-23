# 🔄 PM2 Commands Reference

## 📋 Command Directory

| Command | Description |
|---------|-------------|
| [pm2 start](#pm2-start) | Start application processes |
| [pm2 stop](#pm2-stop) | Stop running processes |
| [pm2 restart](#pm2-restart) | Restart processes |
| [pm2 reload](#pm2-reload) | Gracefully reload processes |
| [pm2 delete](#pm2-delete) | Delete processes from PM2 |
| [pm2 list](#pm2-list) | List all processes |
| [pm2 show](#pm2-show) | Show detailed process information |
| [pm2 logs](#pm2-logs) | View process logs |
| [pm2 monit](#pm2-monit) | Real-time monitoring dashboard |
| [pm2 flush](#pm2-flush) | Clear all log files |
| [pm2 save](#pm2-save) | Save current process list |
| [pm2 resurrect](#pm2-resurrect) | Restore saved processes |
| [pm2 startup](#pm2-startup) | Generate startup script |
| [pm2 ecosystem](#pm2-ecosystem) | Generate ecosystem configuration |
| [pm2 deploy](#pm2-deploy) | Deploy applications |

---

## Process Management

### pm2 start

**What it does:** Starts applications and manages them as background processes

**Syntax:**
```bash
pm2 start <app> [options]
```

**Example Usage:**
```bash
# Start Node.js application
pm2 start app.js

# Start with custom name
pm2 start app.js --name "my-api"

# Start with specific instances (cluster mode)
pm2 start app.js -i 4

# Start with maximum CPU cores
pm2 start app.js -i max

# Start with environment variables
pm2 start app.js --env production

# Start with watch mode (auto-restart on file changes)
pm2 start app.js --watch

# Start with specific interpreter
pm2 start app.py --interpreter python3

# Start with memory limit
pm2 start app.js --max-memory-restart 1000M

# Start from package.json script
pm2 start npm -- start
```

**Important Notes:**
- PM2 automatically assigns process ID and name
- Use `-i` for cluster mode (load balancing)
- Watch mode is great for development but not production
- Memory limit automatically restarts process if exceeded

---

### pm2 stop

**What it does:** Stops running processes without removing them from PM2

**Syntax:**
```bash
pm2 stop <app_name|app_id|all>
```

**Example Usage:**
```bash
# Stop specific process by name
pm2 stop my-api

# Stop specific process by ID
pm2 stop 0

# Stop all processes
pm2 stop all

# Stop multiple processes
pm2 stop app1 app2 app3
```

**Important Notes:**
- Processes remain in PM2 list but are stopped
- Can be restarted with `pm2 start`
- Use `pm2 delete` to completely remove processes

---

### pm2 restart

**What it does:** Restarts processes (stops then starts)

**Syntax:**
```bash
pm2 restart <app_name|app_id|all>
```

**Example Usage:**
```bash
# Restart specific process
pm2 restart my-api

# Restart all processes
pm2 restart all

# Restart with new environment
pm2 restart app.js --env production

# Restart and update environment variables
pm2 restart my-api --update-env
```

**Important Notes:**
- Causes brief downtime during restart
- Use `pm2 reload` for zero-downtime restarts
- Useful for applying configuration changes

---

### pm2 reload

**What it does:** Gracefully reloads processes with zero downtime (cluster mode only)

**Syntax:**
```bash
pm2 reload <app_name|app_id|all>
```

**Example Usage:**
```bash
# Graceful reload
pm2 reload my-api

# Reload all processes
pm2 reload all

# Reload with environment update
pm2 reload my-api --update-env
```

**Important Notes:**
- Only works in cluster mode (`-i` > 1)
- Maintains service availability during reload
- Gradually replaces old processes with new ones

---

### pm2 delete

**What it does:** Completely removes processes from PM2

**Syntax:**
```bash
pm2 delete <app_name|app_id|all>
```

**Example Usage:**
```bash
# Delete specific process
pm2 delete my-api

# Delete by process ID
pm2 delete 0

# Delete all processes
pm2 delete all

# Delete multiple processes
pm2 delete app1 app2
```

**Important Notes:**
- Permanently removes process from PM2 list
- Process must be stopped first or will be force-killed
- Cannot be undone without restarting the application

---

## Process Information

### pm2 list

**What it does:** Displays list of all PM2 processes

**Syntax:**
```bash
pm2 list [options]
```

**Example Usage:**
```bash
# List all processes
pm2 list

# Short alias
pm2 ls

# List in JSON format
pm2 jlist

# List with additional details
pm2 prettylist

# List only running processes
pm2 list | grep online
```

**Important Notes:**
- Shows process ID, name, status, CPU, memory usage
- Status can be: online, stopped, errored, or stopping
- Use regularly to monitor application health

---

### pm2 show

**What it does:** Shows detailed information about a specific process

**Syntax:**
```bash
pm2 show <app_name|app_id>
```

**Example Usage:**
```bash
# Show process details
pm2 show my-api

# Show by process ID
pm2 show 0

# Show in JSON format
pm2 show my-api --json
```

**Important Notes:**
- Displays comprehensive process information
- Includes memory usage, CPU, uptime, restart count
- Shows environment variables and configuration

---

## Logging and Monitoring

### pm2 logs

**What it does:** Displays real-time logs from processes

**Syntax:**
```bash
pm2 logs [app_name|app_id] [options]
```

**Example Usage:**
```bash
# Show logs for all processes
pm2 logs

# Show logs for specific process
pm2 logs my-api

# Show last 100 lines
pm2 logs --lines 100

# Show logs with timestamps
pm2 logs --timestamp

# Show only error logs
pm2 logs --err

# Show logs in JSON format
pm2 logs --json

# Follow logs (real-time)
pm2 logs --follow
```

**Important Notes:**
- Logs are automatically rotated to prevent disk space issues
- Use `Ctrl+C` to exit log viewing
- Logs are stored in `~/.pm2/logs/`

---

### pm2 monit

**What it does:** Opens real-time monitoring dashboard

**Syntax:**
```bash
pm2 monit
```

**Example Usage:**
```bash
# Open monitoring dashboard
pm2 monit
```

**Important Notes:**
- Shows real-time CPU and memory usage
- Displays logs in terminal interface
- Use arrow keys to navigate between processes
- Press `q` to quit monitoring

---

### pm2 flush

**What it does:** Clears all log files

**Syntax:**
```bash
pm2 flush [app_name|app_id]
```

**Example Usage:**
```bash
# Clear all logs
pm2 flush

# Clear logs for specific process
pm2 flush my-api

# Clear logs and confirm
pm2 flush --force
```

**Important Notes:**
- Permanently deletes log files
- Useful for clearing disk space
- Logs will start fresh after flushing

---

## Process Persistence

### pm2 save

**What it does:** Saves current process list for automatic restoration

**Syntax:**
```bash
pm2 save
```

**Example Usage:**
```bash
# Save current process list
pm2 save

# Force save (overwrite existing)
pm2 save --force
```

**Important Notes:**
- Creates snapshot of current PM2 processes
- Required for automatic startup after system reboot
- Saves to `~/.pm2/dump.pm2`

---

### pm2 resurrect

**What it does:** Restores previously saved processes

**Syntax:**
```bash
pm2 resurrect
```

**Example Usage:**
```bash
# Restore saved processes
pm2 resurrect
```

**Important Notes:**
- Restores processes from last `pm2 save`
- Useful after system reboot or PM2 restart
- Processes start with their saved configuration

---

### pm2 startup

**What it does:** Generates system startup script for PM2

**Syntax:**
```bash
pm2 startup [platform]
```

**Example Usage:**
```bash
# Generate startup script (auto-detect platform)
pm2 startup

# Generate for specific platform
pm2 startup systemd

# Generate for Ubuntu/Debian
pm2 startup ubuntu

# Disable startup script
pm2 unstartup
```

**Important Notes:**
- Run the generated command with sudo as instructed
- Ensures PM2 starts automatically on system boot
- Must run `pm2 save` after setting up startup

---

## Configuration

### pm2 ecosystem

**What it does:** Generates ecosystem configuration file

**Syntax:**
```bash
pm2 ecosystem [init]
```

**Example Usage:**
```bash
# Generate ecosystem.config.js
pm2 ecosystem

# Initialize with template
pm2 init

# Start from ecosystem file
pm2 start ecosystem.config.js

# Start specific app from ecosystem
pm2 start ecosystem.config.js --only api
```

**Important Notes:**
- Creates `ecosystem.config.js` configuration file
- Allows complex application configurations
- Supports multiple environments and deployment settings

**Example ecosystem.config.js:**
```javascript
module.exports = {
  apps: [{
    name: 'my-api',
    script: 'app.js',
    instances: 'max',
    env: {
      NODE_ENV: 'development'
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 3000
    }
  }],
  
  deploy: {
    production: {
      user: 'ubuntu',
      host: '192.168.1.100',
      ref: 'origin/main',
      repo: 'git@github.com:user/repo.git',
      path: '/var/www/production',
      'pre-deploy-local': '',
      'post-deploy': 'npm install && pm2 reload ecosystem.config.js --env production',
      'pre-setup': ''
    }
  }
};
```

---

## Deployment

### pm2 deploy

**What it does:** Deploys applications using ecosystem configuration

**Syntax:**
```bash
pm2 deploy <environment> [command]
```

**Example Usage:**
```bash
# Setup deployment
pm2 deploy production setup

# Deploy to production
pm2 deploy production

# Deploy and update
pm2 deploy production update

# Revert deployment
pm2 deploy production revert 1

# List deployments
pm2 deploy production list
```

**Important Notes:**
- Requires ecosystem.config.js with deploy configuration
- Automates git deployment and process management
- Supports rollback to previous deployments

---

## Advanced Commands

**Useful PM2 combinations:**
```bash
# Complete restart workflow
pm2 stop all && pm2 delete all && pm2 start ecosystem.config.js

# Monitor and log simultaneously
pm2 monit & pm2 logs --timestamp

# Backup and restore processes
pm2 save && pm2 kill && pm2 resurrect

# Update PM2 globally
npm install -g pm2@latest && pm2 update
```

**💡 Pro Tips:**
- Use ecosystem files for production deployments
- Set up log rotation to prevent disk space issues
- Monitor memory usage to prevent memory leaks
- Use cluster mode for CPU-intensive applications
- Always `pm2 save` after making changes to ensure persistence 