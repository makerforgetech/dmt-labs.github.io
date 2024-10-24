---
layout: post
title: Dynamic Module Loading in Python
date: 2024-10-24 19:01
categories: [Guides, Software]
tags: [guide, development]
---

In the Modular Biped Project I wanted to achieve a **cleaner, scalable**, and **modular design** where new modules can be added or removed without needing to modify `main.py`. To achieve this I wanted to refactor the initiation logic into **module-specific files**. This approach allows each module to handle its own initialization independently, making `main.py` generic and less dependent on changes as new modules are introduced.

# Dynamically Loading Python Modules: A Flexible Architecture for Configurable Module Loading

In modern applications, flexibility and scalability are key to adapting to new requirements and functionalities. One effective way to manage growing complexity is to design an architecture that dynamically loads modules based on configuration files—enabling the system to remain adaptable without altering core logic. In this article, we will discuss how to build such a system in Python, allowing modules to be dynamically loaded at runtime, based on YAML configuration files. We'll also explore how to extend the system by writing new modules that fit seamlessly into this architecture.

## The Problem: Static Module Loading

In many Python applications, modules are statically imported and initialized. This approach works fine for small applications, but as the number of modules grows, managing them manually in the main script can become a nightmare. Every time a module is added, removed, or modified, you need to update the `main.py` file or other related scripts.

Consider the following scenario:
- You have a robotics system that uses multiple modules like actuators, sensors, and camera systems.
- The system is configurable using YAML files, with some modules needing multiple instances (e.g., multiple servo motors).
- You want the system to automatically load and initialize modules based on whether they are enabled in the configuration files, without having to modify the core application.

## Enter: Dynamic Module Loading

In our approach, we will design an architecture that dynamically loads Python modules based on the contents of YAML configuration files. Each module will be able to accept configuration parameters passed dynamically as `**kwargs`, allowing flexibility in how modules are instantiated and configured.

## The Architecture: Dynamic Module Loader

Our architecture revolves around three key components:
1. **YAML Configuration Files**: Define which modules are enabled and their configuration details.
2. **ModuleLoader Class**: Handles the discovery and dynamic loading of modules based on the YAML configuration.
3. **Module Initialization with `**kwargs`**: Ensures that configuration options are passed dynamically to module constructors.

### Folder Structure

```
my_project/
│
├── config/
│   ├── servos.yml               # Configuration for servo modules
│   └── buzzer.yml               # Configuration for buzzer module
│
├── modules/
│   ├── actuators/
│   │   └── servo.py             # Implementation of the Servo class
│   │
│   ├── sensor/
│   │   └── motion_sensor.py      # Implementation of the MotionSensor class
│   │
│   ├── output/
│   │   ├── buzzer.py            # Implementation of the Buzzer class
│   │   └── speaker.py           # Implementation of the Speaker class (if needed)
│   │
│   └── ...                       # Other module directories as needed
│
├── main.py                       # Entry point for the application
├── module_loader.py              # Contains the ModuleLoader class
└── requirements.txt              # List of dependencies for the project
```

### 1. YAML Configuration Files

Each module has a corresponding YAML configuration file that defines whether the module is enabled and what parameters it should use. For example, a `servos.yml` file might configure multiple servo motor instances:

```yaml
servos:
  enabled: true
  path: "modules/actuators/servo"
  instances:
    - name: "leg_l_hip"
      id: 0
      pin: 9
      range: [0, 180]
      start_pos: 40
    - name: "leg_l_knee"
      id: 1
      pin: 10
      range: [0, 180]
      start_pos: 10
```

In this example, the `servos` module is enabled, and two instances (`leg_l_hip` and `leg_l_knee`) are defined with their respective configurations. The `path` points to where the module is located within the project folder.

### 2. ModuleLoader Class

The `ModuleLoader` class is responsible for reading these YAML files, dynamically loading the modules, and creating instances based on the configuration provided. 

Here’s an implementation of `ModuleLoader`:

```python
import os
import yaml
import importlib.util

class ModuleLoader:
    def __init__(self, config_folder='config'):
        self.config_folder = config_folder
        self.modules = self.load_yaml_files()

    def load_yaml_files(self):
        """Load and parse YAML files from the config folder."""
        config_files = [os.path.join(self.config_folder, f) for f in os.listdir(self.config_folder) if f.endswith('.yml')]
        loaded_modules = []
        for file_path in config_files:
            with open(file_path, 'r') as stream:
                try:
                    config = yaml.safe_load(stream)
                    for module_name, module_config in config.items():
                        if module_config.get('enabled', False):
                            loaded_modules.append(module_config)
                except yaml.YAMLError as e:
                    print(f"Error loading {file_path}: {e}")
        return loaded_modules

    def load_modules(self):
        """Dynamically load and instantiate the modules based on the config."""
        instances = {}  # Use a dictionary to store instances for easy access
        for module in self.modules:
            module_path = module['path']  # e.g., "modules/actuators/servo"
            module_name = module_path.split('/')[-1]  # e.g., "servo"
            instances_config = module.get('instances', [module.get('config')])  # Get all instances or config

            # Dynamically load the module
            spec = importlib.util.spec_from_file_location(module_name, f"{module_path}/{module_name}.py")
            mod = importlib.util.module_from_spec(spec)
            spec.loader.exec_module(mod)

            # Create instances of the module
            for instance_config in instances_config:
                # Pass the instance config to the module's __init__ method as **kwargs
                instance_name = instance_config.get('name')  # Use the instance name as the key
                instance = getattr(mod, module_name.capitalize())(**instance_config)

                # Store the instance in the dictionary
                instances[instance_name] = instance

        return instances  # Return the dictionary of instances

```

#### How It Works:
1. **Loading YAML Files**: The `ModuleLoader` reads all YAML files from the `config` folder and loads only the modules that are marked as `enabled`.
2. **Dynamic Module Loading**: Using Python’s `importlib`, the `ModuleLoader` dynamically loads the Python files based on the module path in the YAML.
3. **Module Initialization**: The `ModuleLoader` passes the configuration for each instance as `**kwargs` to the module’s constructor.

### 3. Module Initialization with `**kwargs`

For each module, the constructor (`__init__` method) should be designed to accept `**kwargs`, which allows it to handle configurations flexibly. Let’s look at an example module for controlling servo motors.

```python
class Servo:
    def __init__(self, **kwargs):
        self.pin = kwargs.get('pin')  # Required, no default
        self.name = kwargs.get('name')  # Required, no default
        self.range = kwargs.get('range', [0, 180])  # Optional with default value
        self.id = kwargs.get('id', 0)  # Optional with default value
        self.start = kwargs.get('start_pos', 50)  # Optional with default value
        print(f"Initializing Servo {self.name} on pin {self.pin} with range {self.range} and start {self.start}")
```

Here, the `**kwargs` argument makes the `Servo` class highly flexible, allowing it to accept any configuration that is passed from the YAML file. Using `kwargs.get()`, we retrieve specific configuration parameters and assign default values when necessary.

### 4. Simplified `main.py`

Here, the `main.py` script is simplified to focus on the dynamic loading and initialization of modules, without needing to hard-code specific modules or configurations. Any changes to modules or configurations are now handled entirely via the YAML files.

If you wanted direct access to the instance created by the model loader, you can do that too! In the `main.py` file, when the `ModuleLoader` loads the modules, we have them ready for use:

```python
from module_loader import ModuleLoader

def main():
    # Load all enabled modules
    module_loader = ModuleLoader(config_folder='config')
    module_instances = module_loader.load_modules()

    # Access instances by name
    my_module = module_instances.get("my_module")
    if my_module:
        my_module.start()  # Assuming there's a start method

if __name__ == '__main__':
    main()

```

Interacting with your module via pubsub is also possible, and avoids adding business logic to the main.py file.

```python
from pubsub import pub
pub.sendMessage('mytopic', data='somedata') # Publish to a topic
pub.subscribe(self.handler_method, 'anothertopic') # subscribe to another topic
```

## Writing a New Module for This Architecture

To add a new module to this architecture, follow these simple steps:

### 1. Create a Python Module

Create a new Python class for your module. Ensure that the `__init__` method accepts `**kwargs` for dynamic configuration.

#### Example: `buzzer.py`

```python
class Buzzer:
    def __init__(self, **kwargs):
        self.pin = kwargs.get('pin')  # Required, no default
        print(f"Initializing Buzzer on pin {self.pin}")
    
    def buzz(self):
        print(f"Buzzer on pin {self.pin} is buzzing!")
```

### 2. Define a YAML Configuration

Create a corresponding YAML configuration file in the `config` folder that specifies the module's configuration. For example, the `buzzer.yml` file might look like this:

```yaml
buzzer:
  enabled: true
  path: "modules/output/buzzer"
  config:
    pin: 26
```

### 3. Load the Module Dynamically

With the `ModuleLoader` system in place, the module will be automatically loaded when the application runs, provided that it is enabled in its YAML configuration. No changes are needed in `main.py` or elsewhere in the core logic.

## Conclusion

By introducing dynamic module loading based on YAML configuration files, we’ve built an architecture that is flexible, scalable, and easy to extend. This approach allows developers to add new modules, enable or disable them, and configure multiple instances of the same module—all without modifying the core application code.

### Key Benefits:
- **Scalability**: Easily add or remove modules without changing the core logic.
- **Flexibility**: Modules can accept any configuration parameters dynamically using `**kwargs`.
- **Maintainability**: Centralized management of configuration via YAML files.

Now, when your system grows or needs new modules, you can simply drop in the new module and corresponding YAML file, making your Python application truly dynamic and adaptable.