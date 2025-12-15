```markdown
# 🚀 Linux Server Process Management with Supervisord

A comprehensive guide for installing, configuring, and managing persistent processes on a Linux server using **Supervisord**.

Supervisord is a robust process control system that allows you to monitor and automatically restart programs, ensuring your background services (like web workers, message queues, or custom scripts) remain running, even after failures.

---

## 🌟 Key Features of this Guide

* **Step-by-Step Installation:** Quick setup on Debian/Ubuntu systems.
* **Program Configuration:** Detailed guidance on creating and defining processes.
* **Runtime Management:** Essential `supervisorctl` commands for controlling processes (start, stop, restart, delete).

---

## 🛠️ I. Installation and Basic Setup

This guide uses `apt` (Debian/Ubuntu) for installation.

### 1. Install Supervisord

Run the following commands to install the package and verify the service status:

```bash
# Update package list
sudo apt update

# Install the supervisor package
sudo apt install supervisor

# Verify the service is running
sudo systemctl status supervisor

```

###2. Configuration StructureSupervisord looks for individual program configurations within the `/etc/supervisor/conf.d/` directory.

The main configuration file (`/etc/supervisor/supervisord.conf`) typically includes this section:

```ini
[include]
files = /etc/supervisor/conf.d/*.conf

```

**All custom program definitions should be placed in separate `.conf` files within the `conf.d/` directory.**

---

##⚙️ II. Configuring a New ProgramTo manage a custom script (e.g., a Python worker named `app_worker.py`), create a configuration file.

###1. Create the Configuration File```bash
sudo nano /etc/supervisor/conf.d/app_worker.conf

```

###2. Program Definition ExampleUse the following template, replacing placeholders with your actual command and paths:

```ini
[program:app_worker]
; The full command to execute your application
command=/usr/bin/python3 /path/to/your/app/app_worker.py 

; Working directory for the application
directory=/path/to/your/app/

; Start the program automatically when Supervisord starts
autostart=true 

; If the program exits, restart it automatically
autorestart=true 

; Run the process as a non-root user for security (HIGHLY RECOMMENDED)
user=app_user 

; Log files configuration
redirect_stderr=true 
stdout_logfile=/var/log/supervisor/app_worker_stdout.log
stderr_logfile=/var/log/supervisor/app_worker_stderr.log

```

---

##💻 III. Runtime Management with `supervisorctl`After creating or modifying configuration files, use `supervisorctl` to apply changes and manage processes.

###1. Load New ConfigurationYou must first tell Supervisord to check for changes (`reread`), and then apply those changes (`update`).

```bash
# Check for new/changed config files in conf.d/
sudo supervisorctl reread 

# Apply the changes (Adds and starts new programs)
sudo supervisorctl update 

```

###2. Essential Management Commands| Action | Command | Description |
| --- | --- | --- |
| **Status** | `sudo supervisorctl status` | Show the status of all managed programs. |
| **Start** | `sudo supervisorctl start app_worker` | Start a specific program. |
| **Stop** | `sudo supervisorctl stop app_worker` | Stop a specific program. |
| **Restart** | `sudo supervisorctl restart app_worker` | Quick stop and start cycle. |
| **Tail Logs** | `sudo supervisorctl tail -f app_worker` | View the live log output. |

###3. Deleting a ProgramTo permanently remove a program from Supervisord control:

1. **Stop the process:**
```bash
sudo supervisorctl stop app_worker

```


2. **Delete the configuration file:**
```bash
sudo rm /etc/supervisor/conf.d/app_worker.conf

```


3. **Update Supervisord:**
```bash
sudo supervisorctl reread
sudo supervisorctl update 
# Output will confirm: 'app_worker: removed process group'

```



---

##🌐 IV. Web Interface (Optional)You can enable a basic web interface for monitoring by uncommenting or adding the following section to your main configuration file (`/etc/supervisor/supervisord.conf`):

```ini
[inet_http_server]
port=*:9001
username=admin
password=secure_password

```

After configuration, restart the main service:

```bash
sudo systemctl restart supervisor

```

You can now access the monitoring dashboard at `http://your_server_ip:9001`.

```

```
