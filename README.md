# Student Management System

A simple Vue.js frontend application for managing student records. It uses browser localStorage so student data remains available after refreshing the page.

## Features

- Add, view, edit, and delete student records
- Validate required fields and email format
- Search by student ID, name, email, or course
- Keep records when the browser is refreshed
- Responsive dashboard design for desktop, tablet, and mobile
- Success notifications and a student details modal

## Technologies

- Vue.js 3
- JavaScript
- HTML
- CSS
- localStorage
- Vite

No backend, API, authentication, database, or additional state-management packages are used.

## Installation

```sh
npm install
```

## Running the project

```sh
npm run dev
```

Open the local URL shown by Vite in your browser.

## Production build

```sh
npm run build
```

The application stores records under the `students` key in localStorage. Sample records are created only when no saved records exist. Clearing browser storage removes all records.

## GitHub preparation

Run these commands after reviewing the project:

```sh
git init
git add .
git commit -m "Create Student Management CRUD System"
git branch -M main
```

Do not run `git push` because the target GitHub repository is not configured.
