# Lambda Code Outside and Inside Event Handler

| **Aspect**                 | **Code Outside the Handler**                       | **Code Inside the Handler**                       |
|----------------------------|----------------------------------------------------|---------------------------------------------------|
| **Execution Timing**       | Runs during the init phase (on cold starts).       | Runs during every Lambda invocation.              |
| **Frequency of Execution** | Executed once per container lifecycle.             | Executed every time the Lambda is invoked.        |
| **Purpose**                | Initialization, resource setup, and reusable code. | Event-specific logic and processing.              |
| **Persistence**            | Persists across multiple invocations (if reused).  | Does not persist; resets after each invocation.   |
| **Examples**               | Database connections, loading libraries/configs.   | Event handling, database queries, business logic. |

## Outside handler function
Executes when lambda is initialized.

For reusable, persistent, or initialization tasks (e.g., database connections, SDK clients, configuration loading).
```python
import pymysql
import os

# Database configuration (using environment variables for security)
DB_HOST = os.environ['DB_HOST']
DB_USER = os.environ['DB_USER']
DB_PASSWORD = os.environ['DB_PASSWORD']
DB_NAME = os.environ['DB_NAME']

# Initialize the database connection outside of the handler
try:
    connection = pymysql.connect(
        host=DB_HOST,
        user=DB_USER,
        password=DB_PASSWORD,
        database=DB_NAME,
        cursorclass=pymysql.cursors.DictCursor
    )
    print("Database connection initialized.")
except Exception as e:
    print(f"Error initializing database connection: {e}")
    connection = None
```

## Event Handler function
Executes every time Lambda is invoked.

For event-specific logic or tasks that must run fresh for every invocation.

```Python
# Lambda event handler
def lambda_handler(event, context):
    global connection
    if not connection:
        return {
            'statusCode': 500,
            'body': 'Database connection not available.'
        }

    try:
        # Example query: Fetching data from a table
        with connection.cursor() as cursor:
            cursor.execute("SELECT * FROM my_table LIMIT 10;")
            results = cursor.fetchall()
            print("Query executed successfully.")
        
        return {
            'statusCode': 200,
            'body': results
        }
    except Exception as e:
        print(f"Error during query execution: {e}")
        return {
            'statusCode': 500,
            'body': 'Error during query execution.'
        }
```