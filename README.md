GoStuds – Project Creation System

🚀 Overview
A robust backend-only API built with Django and Django REST Framework (DRF). This system allows authenticated users to manage internal projects while maintaining strict ownership security and data integrity.

🛠 Setup Instructions

1. Clone & Environment Setup
# Navigate to your project folder
cd gostuds_backend

# Activate the existing virtual environment
myenv\Scripts\activate

2. Install Dependencies
Ensure you have the required packages installed from the requirements.txt file:

pip install -r requirements.txt

3. Environment Variables (.env)
Create a .env file in the root directory and add the following keys:

SECRET_KEY=your_generated_key
DEBUG=True
DB_NAME=your_mysql_db_name
DB_USER=your_username
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=3306

4. Database Migration & Run
# Apply migrations to your MySQL database
python manage.py makemigrations
python manage.py migrate

# Start the development server
python manage.py runserver


📊 Database Schema Explanation
The system leverages a relational structure between the built-in Django User model and a custom Project model.

Project Model
Field	             Type	                Description

id	                 BigAutoField	        Primary Key.

title	             CharField	            The name of the project.

description	         TextField	            Detailed project notes.

status	             CharField	            Choices: active or inactive.

duration_months	     Integer	            Duration in months.

creator	             ForeignKey	            Link to auth.User (the project owner).

created_at	         DateTime	            Automatically set on creation.


🛣 API Endpoint List
All project management endpoints require an Authorization Header: Bearer <access_token>

Authentication
POST /api/register/ - Register a new user account.

POST /api/token/ - Login to receive JWT Access & Refresh tokens.

POST /api/token/refresh/ - Refresh an expired access token.

Project Management
GET /api/projects/ - List all projects (Sorted: Latest first).

POST /api/projects/ - Create a new project (Creator is auto-assigned).

GET /api/projects/<id>/ - Retrieve specific project details.

PUT /api/projects/<id>/ - Update a project (Creator only).

DELETE /api/projects/<id>/ - Delete a project (Creator only).

🔐 Permission & Validation Logic

1. Authentication
The system uses JWT (JSON Web Tokens). We use the IsAuthenticated permission class on all Project endpoints to ensure only registered users can interact with the system.

2. Ownership Authorization
Inside the ProjectDetailView, we perform a manual ownership check:

Logic: if project.creator != request.user:

If a user tries to edit or delete a project they did not create, the API returns a 403 Forbidden status with a meaningful error message.

3. Input Validation
Serializers act as the primary validation layer:

Duration: Validated to ensure it is a positive integer.

3. Input Validation
Serializers act as the primary validation layer:

Duration: Validated to ensure it is a positive integer greater than or equal to one.

Security: The creator field is marked as read_only in the serializer. This prevents users from "spoofing" a different creator during a POST request; the backend always overwrites this field with request.user.
