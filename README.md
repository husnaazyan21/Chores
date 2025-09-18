# Chores
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Chore Scheduler</title>
  <link rel="manifest" href="manifest.json" />
  <style>
    body { font-family: sans-serif; max-width: 600px; margin: auto; padding: 1em; }
    h1 { text-align: center; }
    ul { padding: 0; list-style: none; }
    li { padding: 0.5em; border-bottom: 1px solid #ddd; }
    button { padding: 0.5em 1em; margin-top: 1em; }
  </style>
</head>
<body>
  <h1>Chore Scheduler</h1>
  <ul id="chores"></ul>
  <input id="newChore" placeholder="Add a new chore" />
  <button onclick="addChore()">Add</button>

  <script>
    const chores = JSON.parse(localStorage.getItem('chores') || '[]');
    const list = document.getElementById('chores');
    const input = document.getElementById('newChore');

    function render() {
      list.innerHTML = '';
      chores.forEach((c, i) => {
        const li = document.createElement('li');
        li.textContent = c;
        li.onclick = () => { chores.splice(i,1); save(); };
        list.appendChild(li);
      });
    }

    function addChore() {
      if (!input.value.trim()) return;
      chores.push(input.value.trim());
      input.value = '';
      save();
    }

    function save() {
      localStorage.setItem('chores', JSON.stringify(chores));
      render();
    }

    render();
  </script>
</body>
</html>
{
  "name": "Chore Scheduler",
  "short_name": "Chores",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#ffffff",
  "description": "Reminds you of chores on a fixed schedule",
  "icons": []
}
