---
title: SupervisorD
layout: post
categories:
- devops
---

- [Installation](#installation)
- [Configuration](#configuration)
- [Running Supervisor](#running-supervisor)
  - [Supervisor Configuration Structure](#supervisor-configuration-structure)
- [Controller Processes](#controller-processes)
  - [Reread Configuration and Reload It](#reread-configuration-and-reload-it)
  - [Controlling Tool](#controlling-tool)
  - [Start / Stop Processess](#start--stop-processess)

It's been a while I have been using [Supervisor](http://supervisord.org/) to run my application. It's a great little tool for running and monitoring processes on UNIX-like operating systems. It provides you simple simple, centralized interface to all your applications running on a box. Using Web Interface, you can see health and logs of the applications without even logging in in the box.

---

## Installation

```bash
sudo apt-get install -y supervisor
```

## Configuration

`/etc/supervisor/supervisor.conf`

```bash
[include]
files = /etc/supervisor/conf.d/*.conf

# Enable Web Interface
[inet_http_server]
port = 9001
username = user # Basic auth username
password = pass # Basic auth password
```

**Syntax**

```shell
[program:nodehook] - Define the program to monitor. We'll call it "nodehook".
command - This is the command to run that kicks off the monitored process. We use "node" and run the "http.js" file. If you needed to pass any command line arguments or other data, you could do so here.
directory - Set a directory for Supervisord to "cd" into for before running the process, useful for cases where the process assumes a directory structure relative to the location of the executed script.
autostart - Setting this "true" means the process will start when Supervisord starts (essentially on system boot).
autorestart - If this is "true", the program will be restarted if it exits unexpectedly.
startretries - The number of retries to do before the process is considered "failed"
stderr_logfile - The file to write any errors output.
stdout_logfile - The file to write any regular output.
user - The user the process is run as.
environment - Environment variables to pass to the process.
```

---
**Example Configuration**

`/etc/supervisor/conf.d/node-chada-chutkila.conf`

```bash
[program:node-chadachutkila]
command=node /home/art/apps/chada-chutkila/app.js
directory=/home/art
autostart=true
autorestart=true
startretries=3
stderr_logfile=/var/log/art/chada-chutkila.err.log
stdout_logfile=/var/log/art/chada-chutkila.out.log
user=art
environment=SECRET_PASSPHRASE='this is secret',SECRET_TWO='another secret'
```


---

## Running Supervisor
`sudo service supervisor start`

-----

### Supervisor Configuration Structure
```
supervisor/
├── conf.d
│   └── digi-marketplace-node.conf
└── supervisord.conf
```

-----

## Controller Processes

### Reread Configuration and Reload It

```
supervisorctl reread
supervisorctl update
```

### Controlling Tool
`supervisorctl`

### Start / Stop Processess

```
supervisorctl start <processName>
supervisorctl stop <processName>
```
