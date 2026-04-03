# Laravel REST API Learning Project

This project is designed as a beginner-friendly guide to building a RESTful API using Laravel, covering authentication, CRUD operations, and structured documentation.

## Table of Contents

- [Features](#features)
- [Documentation](#documentation)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [How to Run](#how-to-run)
- [API Base URL](#api-base-url)
- [Endpoint](#example-endpoint)

## Features

- Authentication (Login & Register)
- RESTful CRUD Operations (Create, Read, Update, Delete)
- Structured API Documentation

## Documentation

Detailed documentation is available in the `/docs` folder.
Each module (Auth, User, etc.) contains its own endpoint explanation.

## Tech Stack

- Laravel
- MySQL

## Installation

**1. Clone this project using this command**

```bash
$ git clone https://github.com/Ann0solf/laravel-api-learning-path
```

or
Download the ZIP file by clicking the dropdown button `<> Code` and selecting `Download ZIP`

**2. Run the following command to install all dependencies**

```bash
$ composer install
```

**3. Copy the `.env.example` and rename it to `.env`**

**4. Create key generate by following this command**

```bash
$ php artisan key:generate
```

> [!WARNING]
> This key is used to secure application data such as sessions and tokens. Do not share it publicly.

**5. Configure the database in the `.env` file. The variables are configured as follows:**

```env
DB_CONNECTION=mysql # <== use your own database connection. In this case i'll use mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=myapidocs # <== give name for your own database
DB_USERNAME=root # <== fill the credential database. In this case i use root
DB_PASSWORD= # <== fill the password credential (if you're using password, if it's not leave it blank)
```

**6. Migrate your database by following this command**

```bash
$ php artisan migrate
```

## How to Run

Start the development server:

```bash
$ php artisan serve
```

The API will be available at:

- http://localhost:8000
- http://127.0.0.1:8000

## API Base URL

http://localhost:8000/api

## Example Endpoint

### POST /api/login

Authenticate user and return token.
