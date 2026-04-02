# Step 1 - Project Setup

## 1. Create Laravel Project

```bash
$ laravel new my-api-docs
```

## 2. Install API Scaffold

```bash
$ php artisan install:api
```

## 3. Setup Environment

```bash
$ cp .env.example .env
```

## 4. Generate Application Key

```bash
$ php artisan key:generate
```

## 5. Configure Database

Edit `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=myapidocs
DB_USERNAME=root
DB_PASSWORD=
```

## 6. Run Migration

```bash
$ php artisan migrate
```

## 7. Run the Server

```bash
$ php artisan serve
```

**API is now accessible at:**
http://127.0.0.1:8000
