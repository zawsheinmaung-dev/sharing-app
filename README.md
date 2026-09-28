# Sharing App

A social platform where users post questions, tag them, and discuss them through comments and private messages. Built with Laravel and Vue.

Live demo: https://sharing-app-6vcs.onrender.com

## Features

- Register and log in
- Create, edit and delete questions, with tags
- Filter by tag and search
- Like and save questions
- Comment on questions, edit and delete your own comments
- Profile page with your own questions and saved questions
- Private messaging with read status and last-seen time
- Real-time notifications and messages using Laravel Reverb (WebSockets)
- Profile and gallery images stored on Google Drive

## Tech stack

- PHP 8.2+, Laravel 12
- Inertia.js with Vue 3
- Bootstrap 5, Vite
- PostgreSQL
- Laravel Reverb and Laravel Echo for real-time features
- Google Drive API for image storage

## Requirements

- PHP 8.2 or newer (with the `pdo_pgsql` extension)
- Composer
- Node.js 20 or newer
- PostgreSQL

## Getting started

```bash
git clone https://github.com/zawsheinmaung-dev/sharing-app.git
cd sharing-app

composer install
cp .env.example .env
php artisan key:generate
```

Create an empty PostgreSQL database, then open `.env` and fill in your database details (see the table below). Then:

```bash
php artisan migrate
npm install
```

Run the app in three terminals:

```bash
php artisan serve        # the app, http://localhost:8000
npm run dev              # Vite dev server
php artisan reverb:start # WebSocket server, needed for real-time messaging
```

If you don't need real-time features, you can skip the Reverb terminal and leave `BROADCAST_CONNECTION=log`.

`composer dev` starts the app, queue listener, log viewer and Vite together in one command.

## Environment variables

Copy `.env.example` to `.env` and set these.

**Database**

| Variable | Example |
| --- | --- |
| `DB_CONNECTION` | `pgsql` |
| `DB_HOST` | `127.0.0.1` |
| `DB_PORT` | `5432` |
| `DB_DATABASE` | `sharing_app` |
| `DB_USERNAME` | your PostgreSQL user |
| `DB_PASSWORD` | your PostgreSQL password |

If you use a hosted database such as Neon, you can set a single `DB_URL` instead. Never commit real credentials.

**Real-time (Reverb)**

Set `BROADCAST_CONNECTION=reverb` and add:

```
REVERB_APP_ID=any-number
REVERB_APP_KEY=any-string
REVERB_APP_SECRET=any-string
REVERB_HOST=localhost
REVERB_PORT=8080
REVERB_SCHEME=http

VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
VITE_REVERB_HOST="${REVERB_HOST}"
VITE_REVERB_PORT="${REVERB_PORT}"
VITE_REVERB_SCHEME="${REVERB_SCHEME}"
```

Restart `npm run dev` after changing any `VITE_` value.

**Google Drive (image uploads)**

Profile and gallery images are stored on Google Drive, so these are needed for uploads to work:

```
GOOGLE_DRIVE_CLIENT_ID=
GOOGLE_DRIVE_CLIENT_SECRET=
GOOGLE_DRIVE_REFRESH_TOKEN=
GOOGLE_DRIVE_PROFILE_FOLDER_ID=
GOOGLE_DRIVE_GALLERY_FOLDER_ID=
```

Create an OAuth client in Google Cloud Console, get a refresh token for the Drive API, and copy the folder IDs from the Drive folder URLs.

## Tests

```bash
php artisan test
```

## Deployment

The `Dockerfile` builds an image with Apache and PHP, and runs Apache and the Reverb server together with Supervisor. I deployed it on Render. If you deploy your own copy, change the `ServerName` and the `VITE_REVERB_*` values in the Dockerfile to match your domain.

## Project layout

- `app/Http/Controllers` - controllers for questions, comments, likes, saves, tags, messages and auth
- `app/Services` - service classes, including Google Drive uploads
- `app/Models` - Eloquent models
- `database/migrations` - database schema
- `resources/js` - Vue pages and components
- `routes/web.php` - all routes
