<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>iPhone Simulator</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; -webkit-user-select: none; }
    body {
      background-color: #0b0b0e;
      margin: 0; padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Helvetica Neue", sans-serif;
      display: flex; justify-content: center; align-items: center;
      min-height: 100vh; overflow: hidden;
    }
    .iphone-frame {
      width: 100vw; height: 100vh; max-width: 420px; max-height: 880px;
      background: #000; position: relative; overflow: hidden;
      box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5);
    }
    @media (min-width: 440px) {
      .iphone-frame { height: 90vh; border-radius: 50px; border: 12px solid #222; }
    }

    /* Dynamic Island */
    .dynamic-island {
      position: absolute; top: 10px; left: 50%; transform: translateX(-50%);
      width: 110px; height: 30px; background: #000; border-radius: 20px;
      z-index: 1000; transition: all 0.3s ease;
    }

    /* Status Bar iOS */
    .status-bar {
      position: absolute; top: 0; left: 0; right: 0; height: 48px;
      display: flex; justify-content: space-between; align-items: center;
      padding: 0 24px; color: #fff; font-size: 14px; font-weight: 600; z-index: 999;
    }

    .screen { position: absolute; inset: 0; display: none; flex-direction: column; }
    .screen.active { display: flex !important; }
    
    /* Wallpaper Lockscreen & Homescreen */
    #lock-screen, #home-screen {
      background: url('https://images.weserv.nl/?url=i.pinimg.com/736x/8f/c9/2c/8fc92c42ea9787ffcd6ce4bc18a47ff7.jpg') center/cover no-repeat;
    }

    /* LOCK SCREEN */
    #lock-screen {
      justify-content: space-between; padding: 60px 20px 40px 20px; z-index: 10; cursor: pointer;
    }
    .lock-time { color: #fff; text-align: center; margin-top: 15px; }
    .lock-time .date { font-size: 18px; font-weight: 600; opacity: 0.95; text-shadow: 0 2px 10px rgba(0,0,0,0.3); }
    .lock-time .clock { font-size: 82px; font-weight: 700; line-height: 1; margin-top: 2px; text-shadow: 0 4px 15px rgba(0,0,0,0.3); letter-spacing: -2px; }
    
    .swipe-hint {
      text-align: center; color: #fff; font-size: 13px; font-weight: 500;
      background: rgba(255,255,255,0.2); padding: 10px 22px; border-radius: 25px;
      backdrop-filter: blur(15px); -webkit-backdrop-filter: blur(15px);
      width: fit-content; margin: 0 auto; letter-spacing: 0.3px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.15);
    }

    /* PASSCODE SCREEN */
    #passcode-screen { 
      background: rgba(0,0,0,0.55); 
      backdrop-filter: blur(30px); -webkit-backdrop-filter: blur(30px); 
      justify-content: center; align-items: center; z-index: 20; 
    }
    .passcode-title { color: #fff; font-size: 18px; font-weight: 500; margin-bottom: 24px; }
    .dots { display: flex; gap: 18px; margin-bottom: 45px; }
    .dot { width: 13px; height: 13px; border-radius: 50%; border: 1.5px solid #fff; }
    .dot.active { background: #fff; }
    .keypad { display: grid; grid-template-columns: repeat(3, 1fr); gap: 22px 26px; }
    .key { 
      width: 75px; height: 75px; border-radius: 50%; 
      background: rgba(255,255,255,0.2); color: #fff; 
      font-size: 28px; font-weight: 400; display: flex; align-items: center; justify-content: center; 
      cursor: pointer; backdrop-filter: blur(10px);
    }
    .key:active { background: rgba(255,255,255,0.45); }

    /* HOME SCREEN */
    #home-screen { padding: 52px 16px 20px 16px; z-index: 5; overflow-y: auto; }
    .widgets { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; margin-bottom: 22px; }
    .widget { 
      background: rgba(255,255,255,0.3); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px); 
      border-radius: 24px; padding: 16px; color: #fff; box-shadow: 0 8px 20px rgba(0,0,0,0.12);
    }
    .app-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px 12px; margin-bottom: 80px; }
    .app-item { display: flex; flex-direction: column; align-items: center; gap: 6px; cursor: pointer; }
    .app-icon { 
      width: 62px; height: 62px; border-radius: 15px; 
      display: flex; align-items: center; justify-content: center; 
      font-size: 30px; color: #fff; box-shadow: 0 5px 12px rgba(0,0,0,0.25); 
    }
    .app-label { color: #fff; font-size: 11px; font-weight: 500; text-shadow: 0 1px 4px rgba(0,0,0,0.8); }
    
    /* DOCK iOS */
    .dock { 
      position: absolute; bottom: 18px; left: 16px; right: 16px; height: 90px; 
      background: rgba(255,255,255,0.3); backdrop-filter: blur(30px); -webkit-backdrop-filter: blur(30px); 
      border-radius: 35px; display: flex; justify-content: space-around; align-items: center; padding: 0 12px; 
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
    }

    /* WINDOW APLIKASI */
    .app-window { position: absolute; inset: 0; background: #fff; z-index: 100; display: none; flex-direction: column; }
    .app-window.active { display: flex !important; }
    .app-header { 
      height: 64px; background: #f7f7f8; border-bottom: 1px solid #e5e5ea; 
      display: flex; align-items: center; padding: 22px 16px 0 16px; justify-content: space-between; 
    }
    .back-btn { color: #007aff; font-size: 17px; font-weight: 500; cursor: pointer; display: flex; align-items: center; gap: 4px; }
    .app-content { flex: 1; overflow-y: auto; }

    /* CHAT WHATSAPP */
    .chat-item { display: flex; align-items: center; gap: 14px; padding: 12px 16px; border-bottom: 0.5px solid #e5e5ea; cursor: pointer; }
    .chat-avatar { width: 52px; height: 52px; border-radius: 50%; object-fit: cover; }
    .chat-info { flex: 1; }
    .chat-name { font-weight: 600; font-size: 16px; color: #000; }
    .chat-msg { color: #8e8e93; font-size: 14px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 220px; margin-top: 2px; }
    
    .chat-room { position: absolute; inset: 0; background: #e5ddd5; z-index: 110; display: none; flex-direction: column; }
    .chat-room.active { display: flex !important; }
    .messages-area { flex: 1; padding: 16px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
    .msg-bubble { max-width: 78%; padding: 9px 14px; border-radius: 16px; font-size: 15px; line-height: 1.4; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
    .msg-in { background: #fff; align-self: flex-start; border-top-left-radius: 4px; color: #000; }
    .msg-out { background: #e2ffc7; align-self: flex-end; border-top-right-radius: 4px; color: #000; }
  </style>
</head>
<body>

  <div class="iphone-frame">
    <!-- Dynamic Island & Status Bar -->
    <div class="dynamic-island"></div>
    <div class="status-bar">
      <span id="status-clock">04:34</span>
      <div style="display:flex; gap:6px;">📶 5G 🔋</div>
    </div>

    <!-- LOCK SCREEN iOS -->
    <div id="lock-screen" class="screen active" onclick="showPasscode()">
      <div class="lock-time">
        <div class="date">Rabu, 21 Oktober</div>
        <div class="clock" id="lock-clock">04:34</div>
      </div>
      <div class="swipe-hint">💡 Ketuk Layar Untuk Membuka</div>
    </div>

    <!-- PASSCODE SCREEN -->
    <div id="passcode-screen" class="screen">
      <div class="passcode-title">Masukkan Passcode</div>
      <div class="dots">
        <div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div>
      </div>
      <div class="keypad">
        <div class="key" onclick="pressKey('1')">1</div>
        <div class="key" onclick="pressKey('2')">2</div>
        <div class="key" onclick="pressKey('3')">3</div>
        <div class="key" onclick="pressKey('4')">4</div>
        <div class="key" onclick="pressKey('5')">5</div>
        <div class="key" onclick="pressKey('6')">6</div>
        <div class="key" onclick="pressKey('7')">7</div>
        <div class="key" onclick="pressKey('8')">8</div>
        <div class="key" onclick="pressKey('9')">9</div>
        <div class="key" style="opacity:0"></div>
        <div class="key" onclick="pressKey('0')">0</div>
      </div>
    </div>

    <!-- HOME SCREEN -->
    <div id="home-screen" class="screen">
      <div class="widgets">
        <div class="widget" style="background:rgba(255,255,255,0.85); color:#000;">
          <div style="color:#ff3b30; font-size:12px; font-weight:700;">RABU</div>
          <div style="font-size:34px; font-weight:700;">21</div>
          <div style="font-size:12px; color:#555; margin-top:4px;">• Belanja<br>• Cuci baju</div>
        </div>
        <div class="widget" style="background:rgba(42,130,228,0.85);">
          <div style="font-size:12px; font-weight:600;">Seoul 📍</div>
          <div style="font-size:30px; font-weight:700; margin:2px 0;">18°</div>
          <div style="font-size:11px; opacity:0.9;">Cerah Berawan</div>
        </div>
      </div>

      <div class="app-grid">
        <div class="app-item" onclick="openApp('wa')">
          <div class="app-icon" style="background:#25D366;">💬</div>
          <span class="app-label">WhatsApp</span>
        </div>
        <div class="app-item">
          <div class="app-icon" style="background:linear-gradient(45deg, #f09433, #bc1888);">📸</div>
          <span class="app-label">Instagram</span>
        </div>
        <div class="app-item">
          <div class="app-icon" style="background:#fbc02d;">📝</div>
          <span class="app-label">Catatan</span>
        </div>
        <div class="app-item">
          <div class="app-icon" style="background:#fff;">🖼️</div>
          <span class="app-label">Foto</span>
        </div>
      </div>

      <!-- DOCK BOTTOM -->
      <div class="dock">
        <div class="app-item" onclick="openApp('wa')"><div class="app-icon" style="background:#25D366;">💬</div></div>
        <div class="app-item"><div class="app-icon" style="background:linear-gradient(45deg, #f09433, #bc1888);">📸</div></div>
        <div class="app-item"><div class="app-icon" style="background:#107c41;">✉️</div></div>
        <div class="app-item"><div class="app-icon" style="background:#333;">⚙️</div></div>
      </div>
    </div>

    <!-- WHATSAPP WINDOW -->
    <div id="wa-app" class="app-window">
      <div class="app-header">
        <span class="back-btn" onclick="closeApp('wa')">‹ Home</span>
        <span style="font-weight:600; font-size:17px;">WhatsApp</span>
        <span style="width:40px;"></span>
      </div>
      <div class="app-content" id="chat-list"></div>
    </div>

    <!-- CHAT ROOM WINDOW -->
    <div id="chat-room" class="chat-room">
      <div class="app-header">
        <span class="back-btn" onclick="closeChat()">‹ Chat</span>
        <span id="chat-title" style="font-weight:600; font-size:16px;">Nama</span>
        <span style="width:40px;"></span>
      </div>
      <div class="messages-area" id="messages-container"></div>
    </div>
  </div>

  <script>
    const whatsappChats = [
      {
        id: 1,
        name: "Khaleed خالد♥️",
        avatar: "https://images.weserv.nl/?url=i.pinimg.com/736x/87/4c/f0/874cf0eeef11bb43b832b4bfa293c5d6.jpg",
        messages: [
          { sender: "me", text: "Miss you so much ;(" },
          { sender: "them", text: "😂 لماذا أنت مضحكٌ جداً؟ ههه" },
          { sender: "them", text: "I will go to Indonesia later" }
        ]
      },
      {
        id: 2,
        name: "Mama ❤️",
        avatar: "https://images.weserv.nl/?url=i.pinimg.com/736x/01/be/3e/01be3e46c76dbbeac7b57b11d95079a4.jpg",
        messages: [
          { sender: "them", text: "Jangan lupa makan ya nak ✨" }
        ]
      },
      {
        id: 3,
        name: "Sha 💖",
        avatar: "https://images.weserv.nl/?url=i.pinimg.com/736x/21/d9/39/21d939a3f25c27633e9d80d24ee4be1b.jpg",
        messages: [
          { sender: "them", text: "be, jadi ngga?" }
        ]
      },
      {
        id: 4,
        name: "sekawan-kawan 👥",
        avatar: "https://images.weserv.nl/?url=i.pinimg.com/736x/a2/5b/4b/a25b4b73b5c6e866e4a2cbe7d0571bc8.jpg",
        messages: [
          { sender: "them", text: "Jenna: wkwkwk" }
        ]
      }
    ];

    function updateClock() {
      const now = new Date();
      const h = String(now.getHours()).padStart(2, '0');
      const m = String(now.getMinutes()).padStart(2, '0');
      document.getElementById('status-clock').innerText = `${h}:${m}`;
      document.getElementById('lock-clock').innerText = `${h}:${m}`;
    }
    setInterval(updateClock, 1000); updateClock();

    function showPasscode() {
      document.getElementById('lock-screen').classList.remove('active');
      document.getElementById('passcode-screen').classList.add('active');
    }

    let passCount = 0;
    function pressKey(num) {
      const dots = document.querySelectorAll('.dot');
      if (passCount < 4) {
        dots[passCount].classList.add('active');
        passCount++;
      }
      if (passCount === 4) {
        setTimeout(() => {
          document.getElementById('passcode-screen').classList.remove('active');
          document.getElementById('home-screen').classList.add('active');
          passCount = 0;
          dots.forEach(d => d.classList.remove('active'));
        }, 150);
      }
    }

    function openApp(id) {
      if(id === 'wa') {
        renderChatList();
        document.getElementById('wa-app').classList.add('active');
      }
    }

    function closeApp(id) {
      document.getElementById(`${id}-app`).classList.remove('active');
    }

    function renderChatList() {
      const container = document.getElementById('chat-list');
      container.innerHTML = whatsappChats.map(chat => `
        <div class="chat-item" onclick="openChat(${chat.id})">
          <img src="${chat.avatar}" class="chat-avatar">
          <div class="chat-info">
            <div class="chat-name">${chat.name}</div>
            <div class="chat-msg">${chat.messages[chat.messages.length - 1].text}</div>
          </div>
        </div>
      `).join('');
    }

    function openChat(id) {
      const chat = whatsappChats.find(c => c.id === id);
      document.getElementById('chat-title').innerText = chat.name;
      const msgArea = document.getElementById('messages-container');
      msgArea.innerHTML = chat.messages.map(m => `
        <div class="msg-bubble ${m.sender === 'me' ? 'msg-out' : 'msg-in'}">${m.text}</div>
      `).join('');
      document.getElementById('chat-room').classList.add('active');
    }

    function closeChat() {
      document.getElementById('chat-room').classList.remove('active');
    }
  </script>
</body>
</html>
