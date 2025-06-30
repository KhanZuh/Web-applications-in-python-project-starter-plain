# Flask Web & Database Project Starter

This is a starter project for you to use to start your Flask web & database
projects.

It contains quite a lot of example code. You can use this to see how the various
parts of the project work, or you can delete it and start from scratch.

There are two videos to support:

* [A demonstration of setting up the project](https://www.youtube.com/watch?v=72JwLuAyHyM&t=360s)
* [A walkthrough of the project codebase](https://www.youtube.com/watch?v=72JwLuAyHyM&t=735s)



# Project walkthrough

Imagine you have a digital library where you can:
- See all your books 📚
- Look at one specific book 📖
- Add a new book ➕
- Remove a book you don't want anymore ❌

This codebase is like building that digital library! Here's what each part does:
- The Library Building (Flask App): This is your main building where people come to use your library
- The Librarian (Routes): These are the helpers who know how to find books, add books, etc.
- The Filing System (Database): This is where all your book information is actually stored
- The Book Cards (Book Class): Each book has a card with its title and author
- The Filing Cabinet Manager (Repository): This person knows exactly how to organize and find things in the filing system

## Code Specifics Walkthrough
### 1. Main Application (`app.py`)
This is your Flask web server - the entry point of your application.
```python
from flask import Flask, request
app = Flask(__name__)

# Simple route that returns a smiley face
@app.route('/emoji', methods=['GET'])
def get_emoji():
    return ":)"
```
The app imports example routes and starts a server on port 5001. When someone visits http://localhost:5001/emoji, they get a smiley face!

### 2. Book Model (`lib/book.py`)
This defines what a "Book" looks like in your system:
```python
class Book:
    def __init__(self, id, title, author_name):
        self.id = id           # Unique identifier
        self.title = title     # Book title
        self.author_name = author_name  # Author's name
```

**Key methods:**

- __eq__: Lets you compare two books to see if they're identical
- __repr__: Makes books display nicely when printed (e.g., "Book(1, Invisible Cities, Italo Calvino)")


### 3. Database Repository (`lib/book_repository.py`)
This is the "data access layer" - it handles all database operations:
```python
class BookRepository:
    def all(self):          # Get all books
    def find(self, book_id): # Get one specific book
    def create(self, book):  # Add a new book
    def delete(self, book_id): # Delete a book
```

**Important pattern: Instead of writing SQL directly in your routes, you use this repository. It's like having a specialized librarian who knows exactly where everything is stored.**

### 4. Database Connection (`lib/database_connection.py`)
This handles connecting to your PostgreSQL database. 

**Key features:**
- Environment awareness: Uses different databases for testing vs development
- Connection management: Ensures you're connected before running queries
- Flask integration: The get_flask_database_connection() function creates one connection per request

### 5. API Routes (`example_routes.py`)
This defines your REST API endpoints:
```python
# GET /books - List all books
@app.route('/books', methods=['GET'])
def get_books():
    connection = get_flask_database_connection(app)
    repository = BookRepository(connection)
    return "\n".join([str(book) for book in repository.all()])
```

**The Pattern (used in every route):**
- Get database connection
- Create repository with that connection
- Use repository to interact with data
   Return response

### 6. Database Schema (`seeds/book_store.sql`)
This creates your database table structure:
```sql
CREATE TABLE books (
    id SERIAL PRIMARY KEY,      -- Auto-incrementing ID
    title VARCHAR(255),         -- Book title
    author_name VARCHAR(255)    -- Author name
);
```
Also includes sample data for testing.

### 7. Testing Structure
The tests/ folder contains comprehensive tests:

- Unit tests: Test individual components (Book class, Repository)
- Integration tests: Test routes and database interactions
- Fixtures: conftest.py provides reusable test setup (database connections, web client)

# How it all comes together

1. Request comes in: Someone hits GET /books
2. Route handler runs: Flask calls the get_books() function
3. Database connection: Function gets a connection to the database
4. Repository pattern: Creates a BookRepository with that connection
5. Data retrieval: Repository runs SQL and converts rows to Book objects
6. Response: Books are converted to strings and returned

**This architecture makes your code:**
- Testable: Each component can be tested independently
- Maintainable: Database logic is separated from web logic
- Scalable: Easy to add new features following the same patterns

The beauty is that you can easily extend this - want to add authors? Create an Author class and AuthorRepository following the same pattern!RetryClaude can make mistakes. Please double-check responses.


# Set up

```shell
# Clone the repository to your local machine
; git clone git@github.com:makersacademy/web-applications-in-python-project-starter-plain.git YOUR_PROJECT_NAME

# Or, if you don't have SSH keys set up
; git clone https://github.com/makersacademy/web-applications-in-python-project-starter-plain.git YOUR_PROJECT_NAME

# Enter the directory
; cd YOUR_PROJECT_NAME

# Set up the virtual environment
; python -m venv web-application-starter-venv

# Activate the virtual environment
; source web-application-starter-venv/bin/activate 

# Install dependencies
(web-application-starter-venv); pip install -r requirements.txt
# Read below if you see an error with `python_full_version`

# Create a test and development database
(web-application-starter-venv); createdb YOUR_PROJECT_NAME
(web-application-starter-venv); createdb YOUR_PROJECT_NAME_test

# Open lib/database_connection.py and change the database name to YOUR_PROJECT_NAME
(web-application-starter-venv); open lib/database_connection.py

# Seed the development database
(web-application-starter-venv); python seed_dev_database.py

# Run the tests (with extra logging) - see below if you have any issues
(web-application-starter-venv); pytest -sv

# Run the web server
; python app.py
# Now visit http://localhost:5001/emoji in your browser
```

<br>
<details>
  <summary>I get a <code>ModuleNotFoundError: No module named 'psycopg'</code></summary>
  <br>
If, after activating your <code>venv</code> and installing dependencies, you see this error when running <code>pytest</code>, please deactivate and reactivate your <code>venv</code>. This should solve the problem - if not, contact your coach.
</details>
<br>

If you would like to remove the example code:

```shell
; ./remove_example_code.sh
```



