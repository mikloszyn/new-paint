<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8">
  <title>Paint z logowaniem</title>
  <style>
    body { font-family: Arial; background: #eef; text-align: center; padding: 20px; }
    canvas { border: 2px solid #333; background: white; cursor: crosshair; }
    input, select { margin: 5px; padding: 10px; width: 220px; border-radius: 10px; }
    button {
      margin: 5px;
      padding: 10px 15px;
      background: #4CAF50;
      color: white;
      border: none;
      border-radius: 20px;
      cursor: pointer;
    }
    #paintArea { display: none; }
  </style>
</head>
<body>
  <div id="authBox">
    <h2>Logowanie / Rejestracja</h2>
    <input id="username" placeholder="Nazwa użytkownika"><br>
    <input id="password" type="password" placeholder="Hasło"><br>
    <button onclick="register()">Utwórz konto</button>
    <button onclick="login()">Zaloguj</button>
    <div id="authMessage"></div>
  </div>

  <div id="paintArea">
    <h2>🎨 Paint</h2>
    <canvas id="canvas" width="600" height="400"></canvas><br>

    <label>Kolor: <input type="color" id="color" value="#000000"></label>
    <label>Pędzel: 
      <select id="brushType">
        <option value="circle">Okrągły</option>
        <option value="square">Kwadratowy</option>
        <option value="line">Linia</option>
      </select>
    </label>
    <label>Grubość: <input type="range" id="brushSize" min="1" max="50" value="5"></label><br>

    <button onclick="setBrush()">Pędzel</button>
    <button onclick="toggleEraser()">Gumka</button>
    <label>Grubość gumki: <input type="range" id="eraserSize" min="5" max="100" value="20"></label><br>

    <button onclick="clearCanvas()">Wyczyść</button>
    <button onclick="saveDrawing()">Zapisz rysunek</button>
    <select id="drawingList" onchange="loadDrawing()"></select><br>

    <button onclick="logout()">Wyloguj się</button>
  </div>

  <script>
    let currentUser = null;
    let painting = false;
    let eraser = false;

    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    canvas.addEventListener("mousedown", () => painting = true);
    canvas.addEventListener("mouseup", () => painting = false);
    canvas.addEventListener("mouseleave", () => painting = false);
    canvas.addEventListener("mousemove", draw);

    function draw(e) {
      if (!painting) return;
      const x = e.offsetX;
      const y = e.offsetY;
      const color = eraser ? "#ffffff" : document.getElementById("color").value;
      const type = document.getElementById("brushType").value;
      const size = eraser ? parseInt(document.getElementById("eraserSize").value) : parseInt(document.getElementById("brushSize").value);

      ctx.fillStyle = color;
      ctx.strokeStyle = color;
      ctx.lineWidth = size;

      if (type === "circle") {
        ctx.beginPath();
        ctx.arc(x, y, size / 2, 0, Math.PI * 2);
        ctx.fill();
      } else if (type === "square") {
        ctx.fillRect(x - size / 2, y - size / 2, size, size);
      } else if (type === "line") {
        ctx.beginPath();
        ctx.moveTo(x - size, y);
        ctx.lineTo(x + size, y);
        ctx.stroke();
      }
    }

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    function toggleEraser() {
      eraser = true;
    }

    function setBrush() {
      eraser = false;
    }

    function register() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      if (!user || !pass) return alert("Podaj nazwę i hasło.");
      if (localStorage.getItem("user_" + user)) {
        authMessage.innerText = "Użytkownik już istnieje.";
      } else {
        localStorage.setItem("user_" + user, pass);
        localStorage.setItem("drawings_" + user, JSON.stringify([]));
        authMessage.innerText = "Konto utworzone!";
      }
    }

    function login() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      const storedPass = localStorage.getItem("user_" + user);
      if (storedPass && storedPass === pass) {
        currentUser = user;
        document.getElementById("authBox").style.display = "none";
        document.getElementById("paintArea").style.display = "block";
        loadDrawingList();
      } else {
        authMessage.innerText = "Błędna nazwa lub hasło.";
      }
    }

    function logout() {
      currentUser = null;
      document.getElementById("authBox").style.display = "block";
      document.getElementById("paintArea").style.display = "none";
      clearCanvas();
      drawingList.innerHTML = "";
    }

    function saveDrawing() {
      const name = prompt("Podaj nazwę rysunku:");
      if (name && currentUser) {
        const dataURL = canvas.toDataURL();
        const drawings = JSON.parse(localStorage.getItem("drawings_" + currentUser)) || [];
        drawings.push({ name, data: dataURL });
        localStorage.setItem("drawings_" + currentUser, JSON.stringify(drawings));
        loadDrawingList();
        alert("Rysunek zapisany!");
      }
    }

    function loadDrawingList() {
      const list = document.getElementById("drawingList");
      list.innerHTML = "";
      const drawings = JSON.parse(localStorage.getItem("drawings_" + currentUser)) || [];
      drawings.forEach((drawing, i) => {
        const option = document.createElement("option");
        option.value = i;
        option.text = drawing.name;
        list.appendChild(option);
      });
    }

    function loadDrawing() {
      clearCanvas();
      const index = drawingList.value;
      const drawings = JSON.parse(localStorage.getItem("drawings_" + currentUser)) || [];
      const img = new Image();
      img.src = drawings[index].data;
      img.onload = () => ctx.drawImage(img, 0, 0);
    }
  </script>
</body>
</html>
