chore-calendar/
  ├── index.html
  ├── style.css
  ├── app.js
  ├── manifest.json
  └── service-worker.js

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Chore Calendar</title>
  <link rel="stylesheet" href="style.css" />
  <link rel="manifest" href="manifest.json" />
</head>
<body>
  <header>
    <h1>🗓 Chore Calendar</h1>
    <div id="monthYear"></div>
  </header>

  <main>
    <div id="calendar"></div>
    <div id="tasks">
      <h2>Tasks</h2>
      <ul id="taskList"></ul>
      <button id="addTaskBtn">+ Add Task</button>
    </div>
  </main>

  <script src="app.js"></script>
</body>
</html>

body {
  font-family: Arial, sans-serif;
  background: #fef6f9;
  color: #333;
  max-width: 800px;
  margin: auto;
  padding: 1em;
}
header, main { text-align: center; }
#calendar {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 5px;
  margin: 1em 0;
}
.day {
  padding: 10px;
  background: #fde2e4;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s;
}
.day:hover { background: #fbc4ab; }
button {
  background: #cdb4db;
  border: none;
  padding: 0.5em 1em;
  border-radius: 8px;
  cursor: pointer;
}

const calendar = document.getElementById('calendar');
const monthYear = document.getElementById('monthYear');
const taskList = document.getElementById('taskList');
const addTaskBtn = document.getElementById('addTaskBtn');

let tasks = JSON.parse(localStorage.getItem('tasks') || '{}');
let currentDate = new Date();

function renderCalendar() {
  calendar.innerHTML = '';
  const year = currentDate.getFullYear();
  const month = currentDate.getMonth();
  monthYear.textContent = currentDate.toLocaleString('default', { month: 'long', year: 'numeric' });

  const firstDay = new Date(year, month, 1).getDay();
  const daysInMonth = new Date(year, month + 1, 0).getDate();

  for (let i = 0; i < firstDay; i++) {
    const blank = document.createElement('div');
    calendar.appendChild(blank);
  }

  for (let day = 1; day <= daysInMonth; day++) {
    const div = document.createElement('div');
    div.className = 'day';
    div.textContent = day;
    div.onclick = () => showTasks(`${year}-${month+1}-${day}`);
    calendar.appendChild(div);
  }
}

function showTasks(dateKey) {
  taskList.innerHTML = '';
  const dayTasks = tasks[dateKey] || [];
  dayTasks.forEach((t, i) => {
    const li = document.createElement('li');
    li.textContent = t;
    li.onclick = () => {
      dayTasks.splice(i,1);
      tasks[dateKey] = dayTasks;
      localStorage.setItem('tasks', JSON.stringify(tasks));
      showTasks(dateKey);
    };
    taskList.appendChild(li);
  });
  addTaskBtn.onclick = () => {
    const newTask = prompt('New task:');
    if (newTask) {
      dayTasks.push(newTask);
      tasks[dateKey] = dayTasks;
      localStorage.setItem('tasks', JSON.stringify(tasks));
      showTasks(dateKey);
    }
  };
}

renderCalendar();

{
  "name": "Chore Calendar",
  "short_name": "Chores",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#fef6f9",
  "description": "Track chores with a pastel calendar and reminders.",
  "icons": []
}

self.addEventListener('install', e => {
  e.waitUntil(
    caches.open('chore-cache').then(cache => {
      return cache.addAll(['/', '/index.html', '/style.css', '/app.js']);
    })
  );
});
self.addEventListener('fetch', e => {
  e.respondWith(
    caches.match(e.request).then(response => response || fetch(e.request))
  );
});
