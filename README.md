# Exercise: Enhance To-Do-List with DB (distinguish by user)

## Learning Goals

Upon completing this exercise, you will be able to:

- Implement MongoDB database integration using Mongoose
- Create and manage relationships between collections
- Design and implement proper data schemas
- Handle user-specific data in a REST API
- Apply database operations in API routes

## Introduction

In this exercise, you'll enhance the existing To-Do API by adding database persistence and user support. You'll create MongoDB collections for both users and todos, establish relationships between them, and modify the API to handle user-specific operations.

> [!NOTE]
> This exercise builds upon your previous Todo API implementation. While the structure will be different due to database integration, you can reference your previous implementation for the route logic and error handling patterns. The main difference is that we'll be replacing the in-memory array storage with MongoDB database operations.


Your enhanced API should support these data structures:


```jsx
// User document structure
{
  "_id": "507f1f77bcf86cd799439011", // MongoDB ObjectId
  "name": "John Doe",
  "email": "john@example.com"  // unique
}

// Todo document structure
{
  "_id": "507f1f77bcf86cd799439012", // MongoDB ObjectId
  "userId": "507f1f77bcf86cd799439011", // Reference to User _id
  "name": "Buy groceries",
  "done": false,
  "category": "shopping"
}
```


> [!NOTE]
> Notice that we're using MongoDB's `ObjectId` for document identification instead of UUID. MongoDB automatically generates a unique `_id` field for each document, so we don't need to handle ID generation ourselves. This is different from our previous implementation of the Todo API where we used the `uuid` package to generate unique IDs for our todos. MongoDB's ObjectId provides additional benefits like timestamp information and guaranteed uniqueness across a distributed system, making it a more robust solution for database-driven applications.


## Getting Started

1. [Fork this repository](https://github.com/codevergehq/exercise-to-do-api-with-db)
2. Clone the repository to your computer
3. Open the repository in VS Code
4. Follow instructions

## Instructions

Let's transform our in-memory Todo API into a database-driven application! 🚀

First of all, you can rename the `.env.example` file to `.env` .


> [!NOTE]
> We provided you with `.env.example` because it is a common practice to provide example environment files in projects. This way, developers can see what environment variables are needed without exposing sensitive information like actual database credentials or API keys in version control. If we had simply provided you with `.env` , it would have been included in version control when we push our code to GitHub. This is a security risk since anyone with access to the repository could see our sensitive credentials. That's why we use `.env.example` as a template instead. Your `.gitignore` file will ensure that your `.env` file is not included in version control when you push your code to GitHub later.

The project comes with the following structure and pre-configured dependencies:

```html
todo-api-with-db/
├── database/
│   ├── db.js        # Database connection setup
│   └── seeds.js     # Database seeding script
├── models/
│   ├── User.model.js
│   └── Todo.model.js
├── routes/
│   ├── user.routes.js
│   └── todo.routes.js
├── .env.example
├── .gitignore
├── app.js           # Main application file
└── package.json     # Project dependencies

```

Next up, make sure you run the following command to install all the necessary packages using the following command:

```bash
npm install
```

This will install all dependencies listed in your package.json file.

### Task 1: Implement Database Connection

In `database/db.js`, implement the database connection:

- Use mongoose to connect to MongoDB
- Handle connection errors appropriately
- Log successful connection
- Export the connection function

Test your connection by running the application.

### Task 2: Create User Schema

In `models/User.model.js`, create a Mongoose schema for users that:

- Requires a `name` field (string)
- Requires a unique `email` field
- Includes timestamps
- Validates `email` format
- Prevents duplicate usernames/emails

### Task 3: Create Todo Schema

In `models/Todo.model.js`, create a Mongoose schema for todos that:

- Requires a `name` field
- Has a boolean `done` field (`default: false`)
- Requires a `category` field
- Links to a user using mongoose references
- Includes timestamps

### Task 4: Implement Database Seeding

In `database/seeds.js`, create a seeding script that:

1. Creates test users
2. Creates sample todos for each user
3. Properly handles the relationship between users and their todos
4. Cleans existing data before seeding
5. Closes the database connection when done

### Task 5: Implement User Routes

In `routes/user.routes.js`, implement these endpoints:

1. Create User (`POST /api/users`)
    1. Validate required fields
    2. Handle duplicate email errors
    3. Return appropriate status codes
    
2. Get All Users (`GET /api/users`)
    1. Return only necessary user information
    2. Handle empty results
    
3. Get User by ID (`GET /api/users/:id`)
    1. Handle non-existent users
    2. Return appropriate error messages
    

### Task 6: **Implement Todo Routes**

In `routes/todo.routes.js`, implement these endpoints:

1. Create Todo (`POST /api/users/:userId/todos`)
    1. Validate user exists
    2. Validate todo data
    3. Link todo to user
    
2. Get User's Todos (`GET /api/users/:userId/todos)`
    1. Support filtering by done status (`?done=true/false`)
    2. Support filtering by category (`?category=work`)
    3. Return only todos belonging to the specified user
    
3. Update Todo (`PUT /api/users/:userId/todos/:todoId`)
    1. Verify todo belongs to user
    2. Validate update data
    3. Return updated todo
    
4. Delete Todo (`DELETE /api/users/:userId/todos/:todoId`)
    1. Verify todo belongs to user
    2. Return appropriate status code
    

### Task 7: Error Handling

Implement proper error handling for:

- Invalid MongoDB IDs
- Database connection errors
- Validation errors
- Duplicate key errors
- Non-existent resources

### Task 8: Testing

Create a Bruno collection to test:

1. User Operations

    - Successfully create a new user
    - Fail to create user with invalid email
    - Fail to create user with duplicate email
    - Successfully retrieve all users
    - Successfully retrieve specific user
    - Fail to retrieve non-existent user

1. Todo Operations

    - Successfully create todo for user
    - Fail to create todo with invalid category
    - Fail to create todo for non-existent user
    - Successfully retrieve user's todos
    - Successfully filter todos by done status
    - Successfully filter todos by category
    - Successfully update todo
    - Successfully delete todo
    - Fail to access todo belonging to different user

And here is a list of expected status codes:

- 201: Resource created successfully
- 200: Request successful
- 204: Delete successful (no content)
- 400: Bad request (validation errors)
- 404: Resource not found
- 500: Server error

If you are not returning these status codes, then please adjust your code to reflect this!

## Submission

When you’ve completed all tasks…

Ensure that all of your code is committed:

```bash
git add .
git commit -m "Complete Todo API with MongoDB and User support"
git push origin main
```

Create a pull request in GitHub and submit your URL.

## Frequently Asked Questions (FAQ)


<details>
<summary>How do I handle MongoDB Connection Errors?</summary>

``` jsx
    const connectDB = async () => {
      try {
        await mongoose.connect(process.env.MONGODB_URI);
        console.log('Connected to MongoDB successfully!');
      } catch (error) {
        console.error('MongoDB connection error:', error);
        process.exit(1);
      }
    };
```
</details>



<details>
<summary>How do I implement the relationship between Users and Todos</summary>

``` jsx
    // In Todo.model.js
    const todoSchema = new mongoose.Schema({
      userId: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User',
        required: true
      },
      // ... other fields
    });
```
</details>

    
<details>
<summary>How do I validate the email format</summary>

``` jsx
    // In User.model.js
    email: {
      type: String,
      required: true,
      unique: true,
      trim: true,
      lowercase: true,
      match: [
        /^\S+@\S+\.\S+$/,
        'Please provide a valid email address'
      ]
    }
```
</details>