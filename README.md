<!DOCTYPE html><html lang="cs">
<head>
  <meta charset="UTF-8">
  <title>Login a Chat</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f2f5;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .box, .page {
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
      text-align: center;
      width: 90%;
      max-width: 400px;
    }
    input {
      padding: 10px;
      margin: 10px 0;
      width: 90%;
      border-radius: 6px;
      border: 1px solid #ccc;
    }
    button {
      padding: 10px 20px;
      background: #0077cc;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 10px;
    }
    #page1, #page2, #choosePage {
      display: none;
    }
    .notification {
      position: absolute;
      top: 20px;
      right: 20px;
      cursor: pointer;
    }
    .notification span {
      background: red;
      color: white;
      border-radius: 50%;
      padding: 5px 10px;
      position: absolute;
      top: -10px;
      right: -10px;
    }
  </style>
</head>
<body><div class="box" id="loginBox">
  <h2>Přihlášení</h2>
  <input type="text" id="username" placeholder="Uživatelské jméno">
  <input type="password" id="password" placeholder="Heslo">
  <button onclick="login()">Přihlásit se</button>
</div><div class="box" id="choosePage">
  <h2>Vyber stranu</h2>
  <div id="buttons"></div>
</div><div class="page" id="page1">
  <h2>Chatovací místnost</h2>
  <p>nic</p>
</div><div class="page" id="page2">
  <h2>Správa účtů</h2>
  <div id="adminPanel"></div>
</div><div class="notification" id="notificationBell" onclick="showRequests()" style="display:none">
  Zvoneček <span id="requestCount">0</span>
</div><script>
  const users = {
    "david": "hugo0709",
    "admin11": "1234"
  };

  let currentUser = "";
  let isAdmin11Suspended = false;
  let unblockRequest = false;

  function login() {
    const u = document.getElementById("username").value;
    const p = document.getElementById("password").value;

    if (users[u] && users[u] === p) {
      currentUser = u;
      document.getElementById("loginBox").style.display = "none";
      document.getElementById("choosePage").style.display = "block";

      const btns = document.getElementById("buttons");
      btns.innerHTML = "";

      const btn1 = document.createElement("button");
      btn1.innerText = "Strana 1 (Chat)";
      btn1.onclick = () => showPage("page1");
      btns.appendChild(btn1);

      const btn2 = document.createElement("button");
      btn2.innerText = "Strana 2";
      btn2.onclick = () => {
        showPage("page2");
        loadAdminPanel();
      };
      btns.appendChild(btn2);

      if (currentUser === "david") {
        document.getElementById("notificationBell").style.display = "block";
        updateNotification();
      }
    } else {
      alert("Špatné jméno nebo heslo");
    }
  }

  function showPage(pageId) {
    document.getElementById("choosePage").style.display = "none";
    document.querySelectorAll(".page").forEach(p => p.style.display = "none");
    document.getElementById(pageId).style.display = "block";
  }

  function loadAdminPanel() {
    const panel = document.getElementById("adminPanel");
    panel.innerHTML = "";

    if (currentUser === "david") {
      const btn = document.createElement("button");
      btn.innerText = isAdmin11Suspended ? "Účet admin11 je pozastaven" : "Pozastavit účet admin11";
      btn.disabled = isAdmin11Suspended;
      btn.onclick = () => {
        isAdmin11Suspended = true;
        alert("Účet admin11 byl pozastaven");
        loadAdminPanel();
      };
      panel.appendChild(btn);
    } else if (currentUser === "admin11") {
      if (isAdmin11Suspended) {
        const requestBtn = document.createElement("button");
        requestBtn.innerText = "Požádat o odblokování účtu";
        requestBtn.onclick = () => {
          unblockRequest = true;
          alert("Žádost byla odeslána Davidovi");
        };
        panel.appendChild(requestBtn);
      } else {
        panel.innerText = "Váš účet je aktivní.";
      }
    }
  }

  function updateNotification() {
    document.getElementById("requestCount").innerText = unblockRequest ? "1" : "0";
  }

  function showRequests() {
    if (unblockRequest && currentUser === "david") {
      const confirmUnblock = confirm("admin11 požádal o odblokování. Odblokovat?");
      if (confirmUnblock) {
        isAdmin11Suspended = false;
        unblockRequest = false;
        alert("Účet admin11 byl odblokován");
        updateNotification();
      }
    }
  }
</script></body>
</html>
