# Student Management System

A simple Vue.js CRUD application made for a school assignment. It manages student records in the browser with localStorage.

## Features

- Add student
- View students
- Edit student
- Delete student
- Search student
- Save records in localStorage

## Run the project

```sh
npm install
npm run dev
```

## How CRUD works

- **Create:** Add a student with the form.
- **Read:** Display all students in the table.
- **Update:** Edit a student and choose Update Student.
- **Delete:** Confirm the deletion and remove the student.
- **Search:** Filter students by Student ID, Name, or Course.

The `students` key is used to save the student list in localStorage. Refreshing the browser keeps the saved records.
