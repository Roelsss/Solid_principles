[Read the full article on Hashnode] (https://roel244.hashnode.dev/activity-33-solid-principles)
# Activity 38: SOLID PRINCIPLES


The SOLID principles are a set of design principles aimed at improving software maintainability, readability, and scalability. These principles are particularly useful when designing object-oriented systems and are key to writing clean, modular code. In this article, we’ll explore each of the SOLID principles and demonstrate how they can be applied in a Python Flask application.

# What are the SOLID Principles?

The SOLID principles are:

Single Responsibility Principle (SRP)
Open/Closed Principle (OCP)
Liskov Substitution Principle (LSP)
Interface Segregation Principle (ISP)
Dependency Inversion Principle (DIP)

# 1. Single Responsibility Principle (SRP)

Definition: A class should have only one reason to change, meaning it should have only one job or responsibility.

Example in Flask:

In a Flask application, a view function should not handle both business logic and HTTP response handling. Instead, they should be separated.

//Bad example (violates SRP)
from flask import Flask, jsonify

app = Flask(__name__)

class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

@app.route('/user')
def get_user():
    user = User("John", "john@example.com")
    # Combining logic of retrieving user and creating response
    return jsonify(name=user.name, email=user.email)

//Good example (respects SRP)
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class UserService:
    @staticmethod
    def get_user_info():
        //Logic for fetching user info
        return User("John", "john@example.com")

@app.route('/user')
def get_user():
    user = UserService.get_user_info()
    return jsonify(name=user.name, email=user.email)


Real-World Use Case: In a real-world application, separating concerns ensures that your code is modular, and any change in the user-related logic does not affect the response handling.

# 2. Open/Closed Principle (OCP)

Definition: Software entities (classes, modules, functions) should be open for extension but closed for modification.

Example in Flask:

You can extend functionality without changing the original class.

//Bad example (violates OCP)
class UserService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

class AdminService:
    def get_user_info(self):
        return {"name": "Admin", "email": "admin@example.com"}

//Good example (respects OCP)
class UserService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

class AdminService(UserService):
    def get_user_info(self):
        return {"name": "Admin", "email": "admin@example.com"}

@app.route('/user')
def get_user():
    user_service = UserService()
    return jsonify(user_service.get_user_info())

@app.route('/admin')
def get_admin():
    admin_service = AdminService()
    return jsonify(admin_service.get_user_info())


Real-World Use Case: When a new user type or behavior needs to be introduced, you can create new classes that extend existing ones without changing the original classes.

# 3. Liskov Substitution Principle (LSP)

Definition: Objects of a subclass should be able to replace objects of the superclass without affecting the correctness of the program.

Example in Flask:

//Bad example (violates LSP)
class UserService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

class AdminService(UserService):
    def get_user_info(self):
        # Returning a different data structure
        return {"name": "Admin", "email": "admin@example.com", "role": "admin"}

//Good example (respects LSP)
class UserService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

class AdminService(UserService):
    def get_user_info(self):
        info = super().get_user_info()
        info["role"] = "admin"
        return info


Real-World Use Case: When creating subclasses, ensure they behave consistently with the parent class, so the rest of your system doesn’t break when you substitute one class for another.

# 4. Interface Segregation Principle (ISP)

Definition: Clients should not be forced to depend on interfaces they do not use.

Example in Flask:

//Bad example (violates ISP)
class UserService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

    def update_user_info(self, user_data):
        # Updates user information
        pass

    def delete_user(self):
        # Deletes a user
        pass

class UserProfileService:
    def get_user_info(self):
        return {"name": "John", "profile_picture": "path/to/pic"}

    def update_user_info(self, user_data):
        # Updates user profile
        pass

//Good example (respects ISP)
class UserInfoService:
    def get_user_info(self):
        return {"name": "John", "email": "john@example.com"}

class UserProfileService:
    def get_user_profile(self):
        return {"name": "John", "profile_picture": "path/to/pic"}

@app.route('/user')
def get_user():
    user_service = UserInfoService()
    return jsonify(user_service.get_user_info())

@app.route('/user/profile')
def get_user_profile():
    profile_service = UserProfileService()
    return jsonify(profile_service.get_user_profile())


Real-World Use Case: Breaking down large interfaces into smaller, more focused services makes the code easier to maintain and more flexible.

# 5. Dependency Inversion Principle (DIP)

Definition: High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

Example in Flask:

//Bad example (violates DIP)
class UserService:
    def __init__(self):
        self.database = MySQLDatabase()  # Low-level module

    def get_user_info(self):
        return self.database.get_user_data()

//Good example (respects DIP)
class UserService:
    def __init__(self, database: Database):
        self.database = database  # High-level module depends on abstraction

    def get_user_info(self):
        return self.database.get_user_data()

class MySQLDatabase(Database):
    def get_user_data(self):
        # Fetch data from MySQL
        pass

class MongoDatabase(Database):
    def get_user_data(self):
        # Fetch data from MongoDB
        pass

@app.route('/user')
def get_user():
    database = MySQLDatabase()
    user_service = UserService(database)
    return jsonify(user_service.get_user_info())


Real-World Use Case: By depending on abstractions (like interfaces or abstract base classes), we can easily swap out different database implementations (MySQL, MongoDB, etc.) without changing the business logic.

# Conclusion

By adhering to the SOLID principles, you can design Python Flask applications that are easier to maintain, extend, and test. The principles help reduce code duplication, increase modularity, and promote reusable components, leading to cleaner and more efficient software.
