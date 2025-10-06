# Task Management API

A RESTful API for managing tasks and to-dos with secure user authentication, built with Node.js, Express, MySQL, and JWT.

## 🌟 Features

- **Secure Authentication**: User registration and login with JWT tokens
- **Password Security**: Passwords hashed with bcrypt before storage
- **Complete CRUD Operations**: Create, read, update, and delete tasks
- **Advanced Filtering**: Filter tasks by status, priority, and search keywords
- **Pagination Support**: Efficient data retrieval with customizable page size
- **User Isolation**: Users can only access and manage their own tasks
- **Input Validation**: Comprehensive validation for all endpoints
- **Error Handling**: Consistent error responses with helpful messages

## 🛠️ Tech Stack

- **Backend**: Node.js v14+
- **Framework**: Express.js v5
- **Database**: MySQL v5.7+
- **ORM**: Sequelize v6
- **Authentication**: JSON Web Tokens (JWT)
- **Password Hashing**: bcryptjs
- **Validation**: express-validator
- **Environment Variables**: dotenv

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14 or higher) - [Download](https://nodejs.org/)
- **MySQL** (v5.7 or higher) - [Download](https://www.mysql.com/downloads/)
- **npm** or **yarn** package manager
- **Git** - [Download](https://git-scm.com/)

## 🚀 Installation

### 1. Clone the repository

```bash
git clone https://github.com/inesta573/task-management-api.git
cd task-management-api
```

### 2. Install dependencies

```bash
npm install
```

### 3. Set up environment variables

Create a `.env` file in the root directory:

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=taskmanager
DB_PORT=3306
JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
JWT_EXPIRE=7d
NODE_ENV=development
```

**Important**: Replace `your_mysql_password` with your actual MySQL password!

### 4. Create the MySQL database

Open your MySQL terminal:

```bash
mysql -u root -p
```

Then create the database:

```sql
CREATE DATABASE taskmanager;
EXIT;
```

### 5. Start the server

**Development mode** (with auto-restart):
```bash
npm run dev
```

**Production mode**:
```bash
npm start
```

The server should now be running at `http://localhost:3000`

You should see:
```
MySQL connected successfully
Database synced
Models synced
Server running on port 3000
```

## 📚 API Documentation

### Base URL

**Local Development:**
```
http://localhost:3000/api
```

### Authentication Endpoints

#### Register User

Create a new user account.

**Endpoint:** `POST /api/auth/register`

**Request Body:**
```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "username": "johndoe",
    "email": "john@example.com"
  }
}
```

**Validation Rules:**
- Username: minimum 3 characters
- Email: must be valid email format
- Password: minimum 6 characters

---

#### Login User

Authenticate and receive a JWT token.

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "johndoe",
    "email": "john@example.com"
  }
}
```

**Note**: Save the token! You'll need it for all task operations.

---

### Task Endpoints

**⚠️ Authentication Required**: All task endpoints require a valid JWT token in the Authorization header:

```
Authorization: Bearer YOUR_JWT_TOKEN_HERE
```

---

#### Create Task

Create a new task.

**Endpoint:** `POST /api/tasks`

**Headers:**
```
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "title": "Complete project documentation",
  "description": "Write comprehensive README and API docs",
  "status": "pending",
  "priority": "high"
}
```

**Field Options:**
- `status`: `"pending"` | `"in-progress"` | `"completed"` (default: `"pending"`)
- `priority`: `"low"` | `"medium"` | `"high"` (default: `"medium"`)

**Success Response (201):**
```json
{
  "success": true,
  "task": {
    "id": 1,
    "title": "Complete project documentation",
    "description": "Write comprehensive README and API docs",
    "status": "pending",
    "priority": "high",
    "userId": 1,
    "createdAt": "2025-10-05T10:30:00.000Z",
    "updatedAt": "2025-10-05T10:30:00.000Z"
  }
}
```

---

#### Get All Tasks

Retrieve all tasks for the authenticated user with optional filtering and pagination.

**Endpoint:** `GET /api/tasks`

**Headers:**
```
Authorization: Bearer YOUR_JWT_TOKEN
```

**Query Parameters (all optional):**
- `status` - Filter by status: `pending`, `in-progress`, `completed`
- `priority` - Filter by priority: `low`, `medium`, `high`
- `search` - Search in title and description
- `page` - Page number (default: 1)
- `limit` - Items per page (default: 10)
- `sort` - Sort order: `newest` (default), `oldest`

**Example Request:**
```
GET /api/tasks?status=pending&priority=high&page=1&limit=5&sort=newest
```

**Success Response (200):**
```json
{
  "success": true,
  "count": 2,
  "pagination": {
    "page": 1,
    "limit": 5,
    "total": 2
  },
  "tasks": [
    {
      "id": 1,
      "title": "Complete project documentation",
      "description": "Write comprehensive README and API docs",
      "status": "pending",
      "priority": "high",
      "userId": 1,
      "createdAt": "2025-10-05T10:30:00.000Z",
      "updatedAt": "2025-10-05T10:30:00.000Z"
    },
    {
      "id": 2,
      "title": "Deploy to production",
      "description": "Deploy API to Railway",
      "status": "pending",
      "priority": "high",
      "userId": 1,
      "createdAt": "2025-10-05T11:00:00.000Z",
      "updatedAt": "2025-10-05T11:00:00.000Z"
    }
  ]
}
```

---

#### Get Single Task

Retrieve a specific task by ID.

**Endpoint:** `GET /api/tasks/:id`

**Headers:**
```
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example:**
```
GET /api/tasks/1
```

**Success Response (200):**
```json
{
  "success": true,
  "task": {
    "id": 1,
    "title": "Complete project documentation",
    "description": "Write comprehensive README and API docs",
    "status": "pending",
    "priority": "high",
    "userId": 1,
    "createdAt": "2025-10-05T10:30:00.000Z",
    "updatedAt": "2025-10-05T10:30:00.000Z"
  }
}
```

---

#### Update Task

Update an existing task. Only provided fields will be updated.

**Endpoint:** `PUT /api/tasks/:id`

**Headers:**
```
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

**Request Body (all fields optional):**
```json
{
  "title": "Updated title",
  "description": "Updated description",
  "status": "completed",
  "priority": "medium"
}
```

**Example - Mark task as completed:**
```json
{
  "status": "completed"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "task": {
    "id": 1,
    "title": "Complete project documentation",
    "description": "Write comprehensive README and API docs",
    "status": "completed",
    "priority": "high",
    "userId": 1,
    "createdAt": "2025-10-05T10:30:00.000Z",
    "updatedAt": "2025-10-05T11:45:00.000Z"
  }
}
```

---

#### Delete Task

Permanently delete a task.

**Endpoint:** `DELETE /api/tasks/:id`

**Headers:**
```
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example:**
```
DELETE /api/tasks/1
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Task deleted successfully"
}
```

---

## ⚠️ Error Responses

All errors follow this format:

```json
{
  "success": false,
  "error": "Error message describing what went wrong"
}
```

### HTTP Status Codes

| Code | Description |
|------|-------------|
| `200` | Success |
| `201` | Created (successful POST request) |
| `400` | Bad Request (validation error, missing fields) |
| `401` | Unauthorized (invalid or missing JWT token) |
| `403` | Forbidden (attempting to access another user's resource) |
| `404` | Not Found (resource doesn't exist) |
| `500` | Internal Server Error |

### Common Error Examples

**Missing JWT Token (401):**
```json
{
  "success": false,
  "error": "Not authorized, no token"
}
```

**Invalid Credentials (401):**
```json
{
  "success": false,
  "error": "Invalid credentials"
}
```

**Validation Error (400):**
```json
{
  "success": false,
  "errors": [
    {
      "msg": "Password must be at least 6 characters",
      "param": "password"
    }
  ]
}
```

**Task Not Found (404):**
```json
{
  "success": false,
  "error": "Task not found"
}
```

---

## 📁 Project Structure

```
task-management-api/
├── src/
│   ├── config/
│   │   └── database.js          # Database connection & configuration
│   ├── controllers/
│   │   ├── authController.js    # Authentication logic (register/login)
│   │   └── taskController.js    # Task CRUD operations
│   ├── middleware/
│   │   └── auth.js              # JWT verification middleware
│   ├── models/
│   │   ├── User.js              # User model (Sequelize)
│   │   ├── Task.js              # Task model (Sequelize)
│   │   └── index.js             # Model relationships
│   ├── routes/
│   │   ├── authRoutes.js        # Authentication routes
│   │   └── taskRoutes.js        # Task routes
│   └── server.js                # Application entry point
├── .env                         # Environment variables (not in git)
├── .env.example                 # Example environment variables
├── .gitignore                   # Git ignore file
├── package.json                 # Dependencies and scripts
├── package-lock.json            # Lock file for dependencies
├── LICENSE                      # MIT License
└── README.md                    # This file
```

---

## 🧪 Testing the API

### Using Thunder Client (VS Code Extension)

1. Install **Thunder Client** extension in VS Code
2. Create a new request collection
3. Test the endpoints following the API documentation above

### Using Postman

1. Download [Postman](https://www.postman.com/downloads/)
2. Import the API endpoints
3. Set up environment variables for the base URL and token

### Using cURL

**Register:**
```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "johndoe",
    "email": "john@example.com",
    "password": "password123"
  }'
```

**Login:**
```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

**Create Task:**
```bash
curl -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "title": "My first task",
    "description": "This is a test task",
    "status": "pending",
    "priority": "high"
  }'
```

---

## 🚢 Deployment

### Deploy to Railway

Railway provides free hosting with MySQL database included.

#### Step 1: Sign up for Railway

1. Go to [railway.app](https://railway.app)
2. Sign up with your GitHub account

#### Step 2: Create New Project

1. Click **"New Project"**
2. Select **"Deploy from GitHub repo"**
3. Choose your `task-management-api` repository

#### Step 3: Add MySQL Database

1. Click **"+ New"** in your project
2. Select **"Database"** → **"MySQL"**

#### Step 4: Configure Environment Variables

In your web service → Variables tab, add:

```
PORT=3000
JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
JWT_EXPIRE=7d
NODE_ENV=production
DB_HOST=${{MySQL.MYSQLHOST}}
DB_USER=${{MySQL.MYSQLUSER}}
DB_PASSWORD=${{MySQL.MYSQLPASSWORD}}
DB_NAME=${{MySQL.MYSQLDATABASE}}
DB_PORT=${{MySQL.MYSQLPORT}}
```

#### Step 5: Generate Domain

1. Go to Settings → Domains
2. Click **"Generate Domain"**
3. Your API is now live! 🎉

### Alternative: Deploy to Render

[Render](https://render.com) is another great free option with similar setup process.

---

## 🔒 Security Best Practices

This API implements several security measures:

- ✅ **Password Hashing**: All passwords are hashed with bcrypt before storage
- ✅ **JWT Authentication**: Stateless authentication using JSON Web Tokens
- ✅ **Input Validation**: All inputs are validated and sanitized
- ✅ **CORS Enabled**: Cross-Origin Resource Sharing configured
- ✅ **Environment Variables**: Sensitive data stored in environment variables
- ✅ **User Isolation**: Users can only access their own resources

### Additional Security Recommendations for Production:

- Use HTTPS in production
- Implement rate limiting to prevent abuse
- Add request logging for monitoring
- Use stronger JWT secrets (32+ random characters)
- Implement refresh tokens for better security
- Add email verification for registration
- Set up monitoring and error tracking

---

## 🛠️ Built With

- [Node.js](https://nodejs.org/) - JavaScript runtime
- [Express.js](https://expressjs.com/) - Web framework
- [MySQL](https://www.mysql.com/) - Relational database
- [Sequelize](https://sequelize.org/) - ORM for MySQL
- [JWT](https://jwt.io/) - JSON Web Tokens for authentication
- [bcryptjs](https://www.npmjs.com/package/bcryptjs) - Password hashing
- [express-validator](https://express-validator.github.io/) - Input validation

---

## 🔮 Future Enhancements

Potential features to add:

- [ ] Task categories and tags
- [ ] Due dates and reminders
- [ ] Task sharing and collaboration
- [ ] Email notifications
- [ ] File attachments for tasks
- [ ] Task comments and activity log
- [ ] OAuth integration (Google, GitHub)
- [ ] Task templates
- [ ] Recurring tasks
- [ ] Advanced search with filters
- [ ] Export tasks (CSV, PDF)
- [ ] Dark mode API response
- [ ] WebSocket for real-time updates

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Your Name**

- GitHub: [@inesta573](https://github.com/inesta573)
- LinkedIn: [inesemmataleb](https://linkedin.com/in/inesemmataleb)
- Email: inestaleb75@outlook.com

---

## 🙏 Acknowledgments

- Thanks to the Express.js community for excellent documentation
- Sequelize ORM for simplifying database operations
- JWT for providing secure authentication
- All open-source contributors who made this project possible

---
