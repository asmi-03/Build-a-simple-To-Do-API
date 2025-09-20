# Build-a-simple-To-Do-API
🔹 1. code5.js (API Server)
This file sets up the server:
Imports modules
const express = require('express');
const fs = require('fs');
const path = require('path');
Data file path
const dataPath = path.join(__dirname, 'todos.json');
Load todos
let todos = [];
try {
  if (fs.existsSync(dataPath)) {
    const raw = fs.readFileSync(dataPath, 'utf8');
    todos = JSON.parse(raw);
  }
} catch (err) {
  console.error('Error loading todos:', err.message);
}
👉 Checks if todos.json exists. If yes, reads it and parses it into todos. Otherwise, starts with an empty array.
Setup Express
const app = express();
app.use(express.json());
const PORT = process.env.PORT || 8000;
👉 express.json() lets us read JSON data from POST requests.
GET Route
app.get('/todos', (req, res) => {
  res.json(todos);
});
👉 Returns the list of todos as JSON.
POST Route
app.post('/todos', (req, res) => {
  const newTodo = req.body;
  if (!newTodo || !newTodo.id || !newTodo.task) {
    return res.status(400).json({ error: 'Todo must have id and task' });
  }
  todos.push(newTodo);
  fs.writeFileSync(dataPath, JSON.stringify(todos, null, 2));
  res.status(201).json({ message: 'Todo added successfully', todo: newTodo });
});
👉 Adds a new todo:
Requires both id and task.
Saves the updated list to todos.json.
Returns success message + todo.
Start Server
app.listen(PORT, () => console.log(`✅ Server running at http://localhost:${PORT}`));
👉 Runs the server on port 8000 (or custom if PORT is set).

🔹 2. package.json
Looks good 👍. It defines:
"start": "node code5.js" → run normally.
"dev": "nodemon code5.js" → auto-restart during development.

🔹 3. todos.json
Here’s the problem:
You currently have:
{
  "id": 1,
  "task": "Finish assignment"
}
But your server expects an array of todos (todos.push(...)).
So this should be:
[
  {
    "id": 1,
    "task": "Finish assignment"
  }
]
✅ Test the API
Start the server:
npm install
npm run dev
GET all todos
👉 Open in browser or use Postman:
GET http://localhost:8000/todos
Response:
[
  {
    "id": 1,
    "task": "Finish assignment"
  }
]
POST a new todo
👉 Send JSON in Postman or curl:
POST http://localhost:8000/todos
Content-Type: application/json
{
  "id": 2,
  "task": "Buy groceries"
}
Response:
{
  "message": "Todo added successfully",
  "todo": {
    "id": 2,
    "task": "Buy groceries"
  }
}
