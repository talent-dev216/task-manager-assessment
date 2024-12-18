
## Task-Manager
A full-stack CRUD task management website with authentication system to create and keep track of tasks according to priority level.
Tech stack: Vue.js, Laravel, MySQL
## Features
Task Management: Create, update, and delete tasks effortlessly.

Real-Time Updates: Stay updated with live changes using Vue.js reactive components.

User Authentication: Secure login and registration using Laravel's authentication system.

Search and Filter: Easily find tasks using search and filter options.
## Tech Stack

- Frontend

 Vue.js

 Vue Router

 Axios

- Backend

 Laravel 10

 MySQL/PostgreSQL

 RESTful API
## Installation

Prerequisites

Ensure you have the following installed on your system:

Node.js (v16 or above)

Composer

PHP (v8.0 or above)

MySQL

## Frontend Setup

Navigate to the frontend directory:

    cd frontend

Install dependencies:

    npm install  

Start the development server:
    npm run serve

## Backend Setup
Navigate to the backend directory:

    cd backend

Install dependencies:

    composer install  

Configure the .env file with your database credentials:

    DB_CONNECTION=mysql  
    DB_HOST=127.0.0.1  
    DB_PORT=3306  
    DB_DATABASE=task_management  
    DB_USERNAME=your_username  
    DB_PASSWORD=your_password  

Run migrations to set up the database schema:

    php artisan migrate  

Start the development server:
    php artisan serve
    
