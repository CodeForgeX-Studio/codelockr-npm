# CodeLockr PHP Runtime

Enterprise-grade PHP runtime for executing AES-256-GCM encrypted source code files.

## Installation

```bash
composer require codelockr/runtime
```

## Deployment

Encrypted `.clr.php` files are **never** sent directly to users or exposed as publicly accessible files on the web server. Instead, use a `router.php` as the single entry point, with an `.htaccess` that forwards all traffic to it.

### Directory Structure

```
public/              ← Web root (only this is publicly accessible)
├── .htaccess        ← Forwards all requests to router.php
├── router.php       ← The only public PHP file
└── index.php        ← Optional: alias to router

app/                 ← Outside web root (or protected via .htaccess)
├── vendor/
│   └── codelockr/runtime/
├── modules/
│   ├── dashboard.clr.php
│   ├── api.clr.php
│   └── auth.clr.php
└── composer.json
```

> **Important:** Place `.clr.php` files **outside** the web root or block direct access via `.htaccess`. They are only loaded through `router.php`.

### 1. `.htaccess` — Forward all requests to router.php

Place this file in your web root (`public/`):

```apache
Options -Indexes

RewriteEngine On

# Do not rewrite existing files and directories
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# Forward everything to router.php
RewriteRule ^ router.php [QSA,L]

# Block direct access to .clr.php files (extra protection)
<FilesMatch "\.clr\.php$">
    Order allow,deny
    Deny from all
</FilesMatch>
```

### 2. `router.php` — Central entry point

This is the only public PHP file. It handles all incoming requests and loads the appropriate encrypted module.

```php
<?php
require_once __DIR__ . '/../app/vendor/autoload.php';

// Get the requested path
$requestUri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
$requestUri = rtrim($requestUri, '/') ?: '/';

// Route table: maps URL paths to encrypted modules
$routes = [
    '/'          => __DIR__ . '/../app/modules/home.clr.php',
    '/dashboard' => __DIR__ . '/../app/modules/dashboard.clr.php',
    '/api'       => __DIR__ . '/../app/modules/api.clr.php',
    '/auth'      => __DIR__ . '/../app/modules/auth.clr.php',
];

if (array_key_exists($requestUri, $routes)) {
    require $routes[$requestUri];
} else {
    http_response_code(404);
    echo '404 - Page not found';
}
```

### 3. `example.php` — Loading a single module

Use this if you want to load one specific encrypted file from another script:

```php
<?php
// Load a specific encrypted module
require __DIR__ . '/../app/modules/dashboard.clr.php';
```

## Deployment Checklist

1. Run `composer install` in the `app/` directory
2. Ensure the web root contains **only** `public/`
3. Verify that `.clr.php` files are **not** directly accessible via the browser
4. Test all routes through `router.php`

## Security Features

- AES-256-GCM encryption
- HMAC-SHA256 integrity validation
- Fragmented key reconstruction
- Temporary decryption to memory (no disk writes)
- No `.clr.php` files directly accessible via the web server

## License

MIT License