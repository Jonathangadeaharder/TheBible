# Python Design Patterns

This document outlines essential design patterns commonly used in Python development.

## Behavioral Patterns

### Strategy Pattern
**When to use**: When you need to switch between different algorithms or behaviors at runtime.

```python
from abc import ABC, abstractmethod
from typing import List

# Strategy interface
class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data: List[int]) -> List[int]:
        pass

# Concrete strategies
class BubbleSort(SortStrategy):
    def sort(self, data: List[int]) -> List[int]:
        # Bubble sort implementation
        result = data.copy()
        for i in range(len(result)):
            for j in range(0, len(result) - i - 1):
                if result[j] > result[j + 1]:
                    result[j], result[j + 1] = result[j + 1], result[j]
        return result

class QuickSort(SortStrategy):
    def sort(self, data: List[int]) -> List[int]:
        # Quick sort implementation
        if len(data) <= 1:
            return data
        pivot = data[len(data) // 2]
        left = [x for x in data if x < pivot]
        middle = [x for x in data if x == pivot]
        right = [x for x in data if x > pivot]
        return self.sort(left) + middle + self.sort(right)

# Context class
class Sorter:
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: SortStrategy):
        self._strategy = strategy

    def sort(self, data: List[int]) -> List[int]:
        return self._strategy.sort(data)

# Usage
data = [64, 34, 25, 12, 22, 11, 90]
sorter = Sorter(BubbleSort())
result = sorter.sort(data)
```

### Observer Pattern
**When to use**: When changes in one object need to be notified to others.

```python
from abc import ABC, abstractmethod
from typing import List

# Observer interface
class Observer(ABC):
    @abstractmethod
    def update(self, message: str):
        pass

# Subject interface
class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer):
        pass

    @abstractmethod
    def detach(self, observer: Observer):
        pass

    @abstractmethod
    def notify(self, message: str):
        pass

# Concrete Subject
class NewsAgency(Subject):
    def __init__(self):
        self._observers: List[Observer] = []
        self._news = ""

    def attach(self, observer: Observer):
        if observer not in self._observers:
            self._observers.append(observer)

    def detach(self, observer: Observer):
        self._observers.remove(observer)

    def notify(self, message: str):
        for observer in self._observers:
            observer.update(message)

    def add_news(self, news: str):
        self._news = news
        self.notify(news)

# Concrete Observer
class NewsChannel(Observer):
    def __init__(self, name: str):
        self.name = name

    def update(self, message: str):
        print(f"{self.name} received news: {message}")

# Usage
agency = NewsAgency()
channel1 = NewsChannel("CNN")
channel2 = NewsChannel("BBC")

agency.attach(channel1)
agency.attach(channel2)

agency.add_news("Breaking: Major event occurred!")
```

### Command Pattern
**When to use**: To encapsulate requests as objects, allowing parameterization of clients with queues, requests, and operations.

```python
from abc import ABC, abstractmethod

# Command interface
class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

    @abstractmethod
    def undo(self):
        pass

# Receiver
class Light:
    def __init__(self):
        self._is_on = False

    def turn_on(self):
        self._is_on = True
        print("Light is ON")

    def turn_off(self):
        self._is_on = False
        print("Light is OFF")

# Concrete Commands
class TurnOnCommand(Command):
    def __init__(self, light: Light):
        self._light = light

    def execute(self):
        self._light.turn_on()

    def undo(self):
        self._light.turn_off()

class TurnOffCommand(Command):
    def __init__(self, light: Light):
        self._light = light

    def execute(self):
        self._light.turn_off()

    def undo(self):
        self._light.turn_on()

# Invoker
class RemoteControl:
    def __init__(self):
        self._history: List[Command] = []

    def submit(self, command: Command):
        command.execute()
        self._history.append(command)

    def undo_last(self):
        if self._history:
            last_command = self._history.pop()
            last_command.undo()

# Usage
light = Light()
remote = RemoteControl()

turn_on = TurnOnCommand(light)
turn_off = TurnOffCommand(light)

remote.submit(turn_on)  # Light is ON
remote.submit(turn_off)  # Light is OFF
remote.undo_last()  # Light is ON
```

## Structural Patterns

### Decorator Pattern
**When to use**: To add new functionality to objects dynamically without affecting other objects.

```python
from functools import wraps

# Function decorator
def timing_decorator(func):
    import time
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        print(f"{func.__name__} took {end_time - start_time:.4f} seconds")
        return result
    return wrapper

@timing_decorator
def slow_function():
    import time
    time.sleep(1)
    return "Done"

# Class decorator
def singleton(cls):
    instances = {}
    @wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class DatabaseConnection:
    def __init__(self):
        print("Creating database connection")

# Usage
slow_function()  # Will print execution time

db1 = DatabaseConnection()  # Creates new instance
db2 = DatabaseConnection()  # Returns same instance
print(db1 is db2)  # True
```

### Adapter Pattern
**When to use**: To make incompatible interfaces work together.

```python
from typing import Protocol

# Target interface
class PaymentProcessor(Protocol):
    def pay(self, amount: float) -> bool:
        pass

# Adaptee classes
class Stripe:
    def make_payment(self, cents: int) -> dict:
        # Stripe payment logic
        return {"success": True, "transaction_id": "stripe_123"}

class PayPal:
    def send_money(self, dollars: float, currency: str = "USD") -> str:
        # PayPal payment logic
        if currency == "USD":
            return "paypal_success_456"
        return "paypal_failed"

# Adapter classes
class StripeAdapter(PaymentProcessor):
    def __init__(self, stripe: Stripe):
        self._stripe = stripe

    def pay(self, amount: float) -> bool:
        result = self._stripe.make_payment(int(amount * 100))  # Convert to cents
        return result["success"]

class PayPalAdapter(PaymentProcessor):
    def __init__(self, paypal: PayPal):
        self._paypal = paypal

    def pay(self, amount: float) -> bool:
        transaction_id = self._paypal.send_money(amount)
        return transaction_id.startswith("paypal_success")

# Client code
def process_payment(processor: PaymentProcessor, amount: float):
    if processor.pay(amount):
        print(f"Payment of ${amount} processed successfully")
    else:
        print("Payment failed")

# Usage
stripe_adapter = StripeAdapter(Stripe())
paypal_adapter = PayPalAdapter(PayPal())

process_payment(stripe_adapter, 100.0)
process_payment(paypal_adapter, 100.0)
```

## Creational Patterns

### Factory Pattern
**When to use**: When object creation logic is complex or varies based on conditions.

```python
from enum import Enum
from typing import Dict, Type

class DatabaseType(Enum):
    MYSQL = "mysql"
    POSTGRES = "postgres"
    SQLITE = "sqlite"

# Product interface
class DatabaseConnection:
    def connect(self):
        pass

    def execute_query(self, query: str):
        pass

# Concrete products
class MySQLConnection(DatabaseConnection):
    def connect(self):
        print("Connecting to MySQL database")

    def execute_query(self, query: str):
        print(f"Executing MySQL query: {query}")

class PostgreSQLConnection(DatabaseConnection):
    def connect(self):
        print("Connecting to PostgreSQL database")

    def execute_query(self, query: str):
        print(f"Executing PostgreSQL query: {query}")

class SQLiteConnection(DatabaseConnection):
    def connect(self):
        print("Connecting to SQLite database")

    def execute_query(self, query: str):
        print(f"Executing SQLite query: {query}")

# Factory
class DatabaseFactory:
    _connections: Dict[DatabaseType, Type[DatabaseConnection]] = {
        DatabaseType.MYSQL: MySQLConnection,
        DatabaseType.POSTGRES: PostgreSQLConnection,
        DatabaseType.SQLITE: SQLiteConnection,
    }

    @classmethod
    def create_connection(cls, db_type: DatabaseType) -> DatabaseConnection:
        connection_class = cls._connections.get(db_type)
        if not connection_class:
            raise ValueError(f"Unsupported database type: {db_type}")
        return connection_class()

# Usage
mysql_conn = DatabaseFactory.create_connection(DatabaseType.MYSQL)
mysql_conn.connect()

postgres_conn = DatabaseFactory.create_connection(DatabaseType.POSTGRES)
postgres_conn.connect()
```

### Builder Pattern
**When to use**: To construct complex objects step by step.

```python
from typing import List, Optional

class Computer:
    def __init__(self):
        self.cpu = ""
        self.ram = ""
        self.storage = ""
        self.gpu = ""
        self.os = ""

    def __str__(self):
        return f"Computer(CPU: {self.cpu}, RAM: {self.ram}, Storage: {self.storage}, GPU: {self.gpu}, OS: {self.os})"

class ComputerBuilder:
    def __init__(self):
        self._computer = Computer()

    def set_cpu(self, cpu: str) -> 'ComputerBuilder':
        self._computer.cpu = cpu
        return self

    def set_ram(self, ram: str) -> 'ComputerBuilder':
        self._computer.ram = ram
        return self

    def set_storage(self, storage: str) -> 'ComputerBuilder':
        self._computer.storage = storage
        return self

    def set_gpu(self, gpu: str) -> 'ComputerBuilder':
        self._computer.gpu = gpu
        return self

    def set_os(self, os: str) -> 'ComputerBuilder':
        self._computer.os = os
        return self

    def build(self) -> Computer:
        return self._computer

# Director
class ComputerDirector:
    @staticmethod
    def build_gaming_computer(builder: ComputerBuilder) -> Computer:
        return (builder
                .set_cpu("Intel i9")
                .set_ram("32GB DDR4")
                .set_storage("1TB NVMe SSD")
                .set_gpu("RTX 4090")
                .set_os("Windows 11")
                .build())

    @staticmethod
    def build_office_computer(builder: ComputerBuilder) -> Computer:
        return (builder
                .set_cpu("Intel i5")
                .set_ram("16GB DDR4")
                .set_storage("512GB SSD")
                .set_os("Ubuntu 22.04")
                .build())

# Usage
builder = ComputerBuilder()
gaming_pc = ComputerDirector.build_gaming_computer(builder)
print(gaming_pc)

office_builder = ComputerBuilder()
office_pc = ComputerDirector.build_office_computer(office_builder)
print(office_pc)
```

## Python-Specific Patterns

### Context Manager Pattern
**When to use**: For resource management and setup/teardown operations.

```python
from contextlib import contextmanager
import sqlite3

# Class-based context manager
class DatabaseManager:
    def __init__(self, db_name: str):
        self.db_name = db_name
        self.connection = None

    def __enter__(self):
        self.connection = sqlite3.connect(self.db_name)
        return self.connection

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.connection:
            self.connection.close()

# Function-based context manager
@contextmanager
def temporary_directory():
    import tempfile
    import shutil
    temp_dir = tempfile.mkdtemp()
    try:
        yield temp_dir
    finally:
        shutil.rmtree(temp_dir)

# Usage
with DatabaseManager("example.db") as conn:
    cursor = conn.cursor()
    cursor.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")

with temporary_directory() as temp_dir:
    print(f"Temporary directory: {temp_dir}")
    # Directory is automatically cleaned up
```

## Anti-Patterns to Avoid

### God Object Pattern
```python
# Bad: One class doing everything
class GodObject:
    def handle_user_auth(self): pass
    def process_payments(self): pass
    def manage_inventory(self): pass
    def send_emails(self): pass
    def generate_reports(self): pass

# Good: Separate classes with single responsibilities
class UserAuthenticator: pass
class PaymentProcessor: pass
class InventoryManager: pass
class EmailService: pass
class ReportGenerator: pass
```

## Quick Reference

| Pattern | Problem It Solves | When to Use |
|---------|------------------|-------------|
| Strategy | Switching algorithms | Multiple approaches for same task |
| Observer | Notification system | One-to-many dependencies |
| Command | Encapsulating requests | Undo/redo, queuing |
| Decorator | Adding functionality | Dynamic behavior extension |
| Adapter | Interface compatibility | Integrating incompatible systems |
| Factory | Object creation | Complex instantiation logic |
| Builder | Complex object construction | Step-by-step object creation |

| Python-Specific | Use Case |
|-----------------|----------|
| @property | Computed attributes |
| @classmethod/@staticmethod | Class/utility methods |
| Context managers | Resource management |
| Decorators | Cross-cutting concerns |
| Dataclasses | Simple data containers |