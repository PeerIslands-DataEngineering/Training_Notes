### **Python Logging**

Logging is an essential part of any application as it allows you to record important events and track the flow of the program. Python provides a built-in `logging` module that allows you to log messages with different severity levels. Here's a comprehensive guide on using Python's `logging` module.

---

### **1. Introduction to Logging**

In Python, the `logging` module allows you to log messages to different outputs such as the console, files, or even remote servers. It supports different severity levels like **DEBUG**, **INFO**, **WARNING**, **ERROR**, and **CRITICAL**.

### **2. Basic Logging**

Here’s how to start logging basic messages to the console.

```python
import logging

# Basic configuration
logging.basicConfig(level=logging.DEBUG)  # Setting the log level

# Logging messages
logging.debug("This is a debug message")
logging.info("This is an info message")
logging.warning("This is a warning message")
logging.error("This is an error message")
logging.critical("This is a critical message")
```

- **`logging.basicConfig()`**: Configures the logging system (e.g., log level, output format, etc.).
- The default log level is **WARNING**. If you set `level=logging.DEBUG`, all levels (DEBUG, INFO, WARNING, ERROR, CRITICAL) will be logged.

### **3. Log Levels**

The **logging module** defines the following standard log levels, from lowest to highest:

- **DEBUG**: Detailed information, typically for diagnosing problems.
- **INFO**: General information on the application's state.
- **WARNING**: An indication that something unexpected happened.
- **ERROR**: A more serious problem that caused a failure.
- **CRITICAL**: A very serious error that may stop the program from continuing.

Example:

```python
logging.debug("Debugging")
logging.info("Informational message")
logging.warning("Warning about something")
logging.error("Error occurred")
logging.critical("Critical issue, system failure")
```

### **4. Logging to a File**

You can also log messages to a file by specifying a `filename` in the `basicConfig()`.

```python
import logging

# Configuring logging to log to a file
logging.basicConfig(filename='app.log', level=logging.DEBUG, 
                    format='%(asctime)s - %(levelname)s - %(message)s')

logging.debug("Debug message")
logging.info("Info message")
```

This will log messages to `app.log` with a timestamp and log level.

### **5. Customizing the Log Format**

The `format` parameter in `basicConfig()` lets you customize the log output. Some common formatting options include:

- `%(asctime)s`: Timestamp of the log entry.
- `%(levelname)s`: Log level (e.g., DEBUG, INFO).
- `%(message)s`: The actual log message.

Example:

```python
import logging

logging.basicConfig(filename='app.log', level=logging.DEBUG,
                    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')

logging.info("This is an info message")
```

Output:

```
2025-03-06 14:40:55,117 - root - INFO - This is an info message
```

### **6. Creating Custom Loggers**

You can create your own logger object for more advanced logging configurations.

```python
import logging

# Create a custom logger
logger = logging.getLogger('my_logger')

# Set the log level
logger.setLevel(logging.DEBUG)

# Create a console handler
ch = logging.StreamHandler()
ch.setLevel(logging.INFO)

# Create a file handler
fh = logging.FileHandler('my_log.log')
fh.setLevel(logging.DEBUG)

# Create a formatter and attach it to handlers
formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
ch.setFormatter(formatter)
fh.setFormatter(formatter)

# Add the handlers to the logger
logger.addHandler(ch)
logger.addHandler(fh)

# Log messages
logger.debug("This is a debug message")
logger.info("This is an info message")
logger.error("This is an error message")
```

- **`getLogger()`**: Creates a named logger. This allows you to have multiple loggers for different parts of the application.
- **Handlers**: We use **StreamHandler** for logging to the console and **FileHandler** for logging to a file.
- **Formatter**: Customize how the log message is displayed.

### **7. Logging Exceptions**

You can log exceptions using the `exception()` method, which is helpful in debugging errors.

```python
import logging

try:
    result = 10 / 0
except ZeroDivisionError as e:
    logging.exception("Exception occurred")
```

This will log the exception with a traceback, making it easier to debug.

### **8. Logging in Multi-threaded Applications**

In a multi-threaded environment, you might want to log messages from multiple threads. The `logging` module supports this by default, and each thread can log its own messages independently.

```python
import logging
import threading

def log_messages():
    logging.info("This is a message from thread")

# Basic logging configuration
logging.basicConfig(level=logging.INFO)

# Create threads
threads = []
for _ in range(5):
    t = threading.Thread(target=log_messages)
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

### **9. Advanced Logging with Loggers, Handlers, and Formatters**

You can create advanced logging setups by combining multiple handlers (e.g., file, console, email) and formatters to log events to different destinations.

```python
import logging

# Create a logger
logger = logging.getLogger('advanced_logger')
logger.setLevel(logging.DEBUG)

# Create handlers
console_handler = logging.StreamHandler()
file_handler = logging.FileHandler('advanced_log.log')

# Set levels for handlers
console_handler.setLevel(logging.WARNING)
file_handler.setLevel(logging.DEBUG)

# Create a formatter and attach it to handlers
formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
console_handler.setFormatter(formatter)
file_handler.setFormatter(formatter)

# Add handlers to logger
logger.addHandler(console_handler)
logger.addHandler(file_handler)

# Log some messages
logger.debug("This is a debug message")
logger.info("This is an info message")
logger.warning("This is a warning message")
logger.error("This is an error message")
```

### **10. Rotating Logs**

To prevent log files from growing indefinitely, you can use `RotatingFileHandler`, which keeps the file size in check by rotating old logs.

```python
from logging.handlers import RotatingFileHandler

# Create a rotating file handler
handler = RotatingFileHandler('app.log', maxBytes=2000, backupCount=5)
handler.setLevel(logging.DEBUG)

# Create a formatter and add it to the handler
formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)

# Create a logger and add the handler
logger = logging.getLogger('rotating_logger')
logger.setLevel(logging.DEBUG)
logger.addHandler(handler)

# Log some messages
for i in range(100):
    logger.debug(f"Logging message {i}")
```

- **`maxBytes`**: The maximum size of the log file before it is rotated.
- **`backupCount`**: The number of backup files to keep. Older files will be deleted after the number exceeds this count.

### **Conclusion**

Python's `logging` module is a powerful and flexible way to log messages in your applications. By using different log levels, handlers, and formatters, you can efficiently track and debug your application. The key steps are:

1. Use `logging.basicConfig()` for simple configurations.
2. Use handlers for more advanced logging to different outputs.
3. Customize log format for better readability.
4. Use `RotatingFileHandler` to manage log files efficiently.
