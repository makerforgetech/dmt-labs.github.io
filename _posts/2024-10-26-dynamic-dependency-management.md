---
layout: post
title: Dynamic Dependency Management for Modular Architecture
date: 2024-10-26 20:44
category: Guides
tags: [guide, dependencies, modular, architecture, automation, python, unix]
---

In modern software development, managing dependencies and configuring environments can often be a tedious and error-prone task. To simplify this process, we've revamped our install.sh script, which automates the installation of Python and Unix dependencies for our modular architecture. This enhanced script not only sets up a Python virtual environment but also intelligently reads configuration files to install dependencies only for active modules, streamlining the setup process. 

## Advantages of Dynamic Dependency Management

1. **Modular Architecture**: Our system is designed to be modular, with each component encapsulated in a separate module. This approach allows for easier maintenance, testing, and scalability.
2. **Dependency Isolation**: By specifying dependencies at the module level, we can ensure that each module has the necessary dependencies without affecting other modules.
3. **Automated Installation**: The enhanced `install.sh` script automates the installation of dependencies based on the configuration files, reducing manual intervention and potential errors. Because only dependencies from active modules are installed, the setup process is optimized and efficient.

## Background

To handle both **Python** and **Unix (system)** dependencies, we’ll make a few updates:

1. **Add a `dependencies` section** to each module's YAML configuration for listing Unix dependencies.
2. Use **Python** (specifically the `yaml` library) in the `install.sh` script to read dependencies from each config file.
3. Ensure **`apt-get install`** is used for Unix dependencies, and **`pip install`** for Python dependencies.

Here's what this would look like:

### Updated YAML Config Files

Each module config can now specify both Python and Unix dependencies.

**Example `servos.yml`:**

```yaml
servos:
  port: /dev/ttyAMA0
  conf:
    leg_l_hip: { id: 0, pin: 9, range: [0, 180], start: 40 }
    # other servo configurations...
  dependencies:
    python:
      - pyserial
      - pigpio
    unix:
      - libpigpio-dev
```

**Example `motion.yml`:**

```yaml
motion:
  pin: 26
  dependencies:
    python:
      - RPi.GPIO
    unix:
      - wiringpi
```

### Modified `install.sh` Script

The install script below:
1. Parses `python` and `unix` dependencies from each module's YAML file.
2. Installs Unix dependencies with `apt-get install` and Python dependencies with `pip install`.
3. Uses a **Python helper** embedded within the script to read YAML files (using `pyyaml`).

Here’s the modified `install.sh` script:

```bash
#!/bin/bash

# Set up Python virtual environment
python3 -m venv --system-site-packages myenv
source myenv/bin/activate

# Initialize arrays for dependencies and active module names
PYTHON_DEPENDENCIES=()
UNIX_DEPENDENCIES=()
ACTIVE_MODULES=()

# Helper function to parse dependencies from YAML files using Python
parse_dependencies() {
  myenv/bin/python3 - <<EOF
import yaml, sys, os

config_file = "$1"
module_name = os.path.basename(config_file).replace('.yml', '')  # Get the module name from the filename
try:
    with open(config_file) as f:
        config = yaml.safe_load(f)
        # Check if the top level of the config is a dictionary to avoid AttributeError
        if isinstance(config, dict):
            for section in config.values():
                # Check if module is active before parsing dependencies
                if isinstance(section, dict) and section.get('enabled') is True:
                    print(f"MODULE:{module_name}")
                    if 'dependencies' in section:
                        for dep_type, deps in section['dependencies'].items():
                            if dep_type == 'python':
                                for dep in deps:
                                    print(f"PYTHON:{dep}")
                            elif dep_type == 'unix':
                                for dep in deps:
                                    print(f"UNIX:{dep}")
except yaml.YAMLError as e:
    print(f"Error reading {config_file}: {e}", file=sys.stderr)
EOF
}

# Iterate over each YAML config file in the config directory
for config_file in config/*.yml; do
  while IFS= read -r dependency; do
    # Separate Python and Unix dependencies and capture active module names
    if [[ $dependency == MODULE:* ]]; then
      ACTIVE_MODULES+=("${dependency#MODULE:}")
    elif [[ $dependency == PYTHON:* ]]; then
      PYTHON_DEPENDENCIES+=("${dependency#PYTHON:}")
    elif [[ $dependency == UNIX:* ]]; then
      UNIX_DEPENDENCIES+=("${dependency#UNIX:}")
    fi
  done < <(parse_dependencies "$config_file")
done

# Remove duplicate dependencies
UNIQUE_PYTHON_DEPENDENCIES=($(echo "${PYTHON_DEPENDENCIES[@]}" | tr ' ' '\n' | sort -u | tr '\n' ' '))
UNIQUE_UNIX_DEPENDENCIES=($(echo "${UNIX_DEPENDENCIES[@]}" | tr ' ' '\n' | sort -u | tr '\n' ' '))
UNIQUE_ACTIVE_MODULES=($(echo "${ACTIVE_MODULES[@]}" | tr ' ' '\n' | sort -u | tr '\n' ' '))

# Update apt-get and install Unix dependencies
if [ ${#UNIQUE_UNIX_DEPENDENCIES[@]} -ne 0 ]; then
  sudo apt-get update
  for dep in "${UNIQUE_UNIX_DEPENDENCIES[@]}"; do
    sudo apt-get install -y "$dep"
  done
fi

# Install Python dependencies explicitly using the virtual environment's pip
for dep in "${UNIQUE_PYTHON_DEPENDENCIES[@]}"; do
  myenv/bin/python3 -m pip install "$dep"
done

# Set execute permissions for additional scripts
chmod 777 startup.sh stop.sh

# Summary of modules and dependencies installed
echo -e "\n==== Installation Summary ===="
echo "Active modules installed: ${#UNIQUE_ACTIVE_MODULES[@]}"
for module in "${UNIQUE_ACTIVE_MODULES[@]}"; do
  echo " - $module"
done

echo -e "\nPython dependencies installed:"
for dep in "${UNIQUE_PYTHON_DEPENDENCIES[@]}"; do
  echo " - $dep"
done

echo -e "\nUnix dependencies installed:"
for dep in "${UNIQUE_UNIX_DEPENDENCIES[@]}"; do
  echo " - $dep"
done
echo "============================="

```

### Explanation of Changes

1. **`parse_dependencies` Function**: A Python function is embedded in the `install.sh` script to parse YAML files and print dependencies in a formatted way for easy processing.
2. **Dependency Arrays**: 
    - `PYTHON_DEPENDENCIES` and `UNIX_DEPENDENCIES` store dependencies, allowing us to separate out `apt-get` and `pip` installations.
3. **Removing Duplicates**: Using `sort -u` to ensure dependencies aren’t installed more than once.
4. **Installing Dependencies**:
    - **Unix** dependencies are updated and installed using `apt-get`.
    - **Python** dependencies are installed with `pip`.

### Folder Structure Example

Here’s the directory structure for this configuration:

```plaintext
project_root/
├── config/
│   ├── servos.yml
│   ├── motion.yml
├── install.sh
└── main.py
```

This approach ensures that dependencies are handled based on each module’s needs, making it easier to maintain and update dependencies dynamically.

### Example Output

After running the `install.sh` script, you should see a summary of the installed modules and dependencies.

Remember, only **active modules** will have their dependencies installed.

```plaintext
==== Installation Summary ====
Active modules installed: 12
 - animate
 - braillespeak
 - buzzer
 - motion
 - neopixel
 - piservo
 - pitemperature
 - serial
 - servos
 - tracking
 - translator
 - vision

Python dependencies installed:
 - adafruit-circuitpython-seesaw
 - googletrans==3.1.0a0
 - gpiozero
 - pigpio
 - pypubsub
 - python3-munkres
 - python3-opencv

Unix dependencies installed:
 - imx500-all
=============================
```

## Read More

The initial modular architecture for this approach was detailed in an earlier blog post. You can read more about it here: [Dynamic Module Loading in Python](/posts/dynamic-module-loading-python/).