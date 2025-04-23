<!DOCTYPE html>
<html lang="cs">
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

    .chat-container {
      width: 100%;
      max-width: 500px;
      height: 70vh;
      border-radius: 16px;
      display: flex;
      flex-direction: column;
    }
    .messages {
      flex: 1;
      padding: 15px;
      overflow-y: auto;
      border: 1px solid #ddd;
      margin-bottom: 10px;
      border-radius: 8px;
      background: #f9f9f9;
    }
    .input-area {
      display: flex;
      gap: 10px;
    }
    .input-area input {
      flex: 1;
    }
    .name-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.4);
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .name-box {
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.3);
      text-align: center;
    }
  </style>
</head>
<body>

<div class="box" id="loginBox">
  <h2>Přihlášení</h2>
  <input type="text" id="username" placeholder="Uživatelské jméno">
  <input type="password" id="password" placeholder="Heslo">
  <button onclick="login()">Přihlásit se</button>
</div>

<div class="box" id="choosePage">
  <h2>Vyber stranu</h2>
  <div id="buttons"></div>
</div>

<div class="page" id="page1">
  <h2>Chatovací místnost</h2>
  <p>nic</p>
  <div class="chat-container">
    <div class="messages" id="messages"></div>
    <div class="input-area">
      <input type="text" id="msgInput" placeholder="Napiš zprávu...">
      <button onclick="sendMessage()">Odeslat</button>
    </div>
  </div>
  <div class="name-overlay" id="nameOverlay">
    <div class="name-box">
      <h3>Zadej své jméno do chatu</h3>
      <input type="text" id="nameInput" placeholder="Tvé jméno">
      <br>
      <button onclick="setName()">Potvrdit</button>
    </div>
  </div>
</div>

<div class="page" id="page2">
  <h2>Napiš Hugo</h2>
  <div id="suspendPanel" style="display: none; margin-top: 20px;">
    <p id="accountStatus">Účet admin11 je aktivní.</p>
    <button onclick="suspendAccount()">Pozastavit účet admin11</button>
  </div>
</div>

<script>
  const users = {
    "david": "hugo0709",
    "admin11": "1234"
  };

  let currentUser = "";
  let chatName = localStorage.getItem("chatName") || "";
  let isAdminSuspended = localStorage.getItem("admin11Suspended") === "true";

  function login() {
    const u = document.getElementById("username").value;
    const p = document.getElementById("password").value;

    if (users[u]) {
      if (u === "admin11" && isAdminSuspended) {
        alert("Účet admin11 je pozastaven.");
        return;
      }
      if (users[u] === p) {
        currentUser = u;
        document.getElementById("loginBox").style.display = "none";
        showPageSelection(u);
        return;
      }
    }
    alert("Špatné jméno nebo heslo");
  }

  function showPageSelection(user) {
    document.getElementById("choosePage").style.display = "block";
    const btns = document.getElementById("buttons");
    btns.innerHTML = "";

    const btn1 = document.createElement("button");
    btn1.innerText = "Strana 1 (Chat)";
    btn1.onclick = () => showPage("page1");
    btns.appendChild(btn1);

    const btn2 = document.createElement("button");
    btn2.innerText = "Strana 2";
    btn2.onclick = () => showPage("page2");
    btns.appendChild(btn2);
  }

  function showPage(pageId) {
    document.getElementById("choosePage").style.display = "none";
    document.querySelectorAll(".page").forEach(p => p.style.display = "none");
    document.getElementById(pageId).style.display = "block";

    if (pageId === "page1" && !chatName) {
      document.getElementById("nameOverlay").style.display = "flex";
    }

    if (pageId === "page2") {
      const panel = document.getElementById("suspendPanel");
      if (currentUser === "david") {
        panel.style.display = "block";
        updateSuspendPanel();
      } else {
        panel.style.display = "none";
      }
    }
  }

  function setName() {
    const name = document.getElementById("nameInput").value.trim();
    if (name.length > 1) {
      chatName = name;
      localStorage.setItem("chatName", chatName);
      document.getElementById("nameOverlay").style.display = "none";
    } else {
      alert("Zadej platné jméno.");
    }
  }

  function sendMessage() {
    const msgBox = document.getElementById("msgInput");
    const message = msgBox.value.trim();
    if (message !== "") {
      const msgList = document.getElementById("messages");
      const div = document.createElement("div");
      div.className = "message";
      div.innerHTML = `<strong>${chatName}:</strong> ${message}`;
      msgList.appendChild(div);
      msgBox.value = "";
      msgList.scrollTop = msgList.scrollHeight;
    }
  }

  function suspendAccount() {
    if (currentUser === "david") {
      isAdminSuspended = !isAdminSuspended;
      localStorage.setItem("admin11Suspended", isAdminSuspended);
      updateSuspendPanel();
    }
  }

  function updateSuspendPanel() {
    const statusText = document.getElementById("accountStatus");
    const button = document.querySelector("#suspendPanel button");

    if (isAdminSuspended) {
      statusText.textContent = "Účet admin11 je pozastaven.";
      button.textContent = "Obnovit účet admin11";
    } else {
      statusText.textContent = "Účet admin11 je aktivní.";
      button.textContent = "Pozastavit účet admin11";
    }
  }
</script>

</body>
</html>
