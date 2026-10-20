<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Special Story for You ❤️</title>
  <style>
    /* RESET & STYLES DASAR */
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background: linear-gradient(135deg, #ffe6ea 0%, #fed1d6 100%);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }

    /* CARD UTAMA */
    .card {
      background: #ffffff;
      width: 100%;
      max-width: 400px;
      padding: 30px 25px;
      border-radius: 24px;
      box-shadow: 0 10px 30px rgba(220, 100, 120, 0.15);
      text-align: center;
      transition: all 0.3s ease;
      position: relative;
      overflow: hidden;
    }

    /* PROFILE PICTURE */
    .profile-img {
      width: 110px;
      height: 110px;
      border-radius: 50%;
      object-fit: cover;
      margin: 0 auto 15px auto;
      border: 4px solid #ff8fa3;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }

    /* TEKS & TITLE */
    .title {
      font-size: 1.4rem;
      color: #ff4d6d;
      margin-bottom: 8px;
      font-weight: 700;
    }

    .subtitle {
      font-size: 0.9rem;
      color: #888;
      margin-bottom: 20px;
    }

    .story-text {
      font-size: 1rem;
      color: #4a4a4a;
      line-height: 1.6;
      margin-bottom: 25px;
      min-height: 60px; /* Menjaga ukuran card tetap stabil */
    }

    /* TOMBOL INTI */
    .btn-container {
      display: flex;
      gap: 12px;
      justify-content: center;
      flex-wrap: wrap;
    }

    .btn {
      background-color: #ff758f;
      color: white;
      border: none;
      padding: 12px 24px;
      border-radius: 30px;
      font-size: 0.95rem;
      font-weight: 600;
      cursor: pointer;
      transition: transform 0.2s, background-color 0.2s;
      outline: none;
    }

    .btn:hover {
      background-color: #ff4d6d;
      transform: translateY(-2px);
    }

    .btn:active {
      transform: translateY(0);
    }

    .btn-secondary {
      background-color: #f0f0f0;
      color: #666;
    }

    .btn-secondary:hover {
      background-color: #e0e0e0;
    }

    /* FADE ANIMATION */
    .fade-in {
      animation: fadeIn 0.4s ease-in-out forwards;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(8px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>

  <div class="card" id="card">
    <!-- Foto Profile (Ganti URL Gambar di bawah) -->
    <img src="https://via.placeholder.com/150" alt="Profile" class="profile-img" id="profileImg">
    
    <!-- Nama / Header -->
    <div class="title" id="profileName">Soraia ✨</div>
    <div class="subtitle" id="profileSub">@username</div>

    <!-- Teks Cerita / Alur -->
    <div class="story-text fade-in" id="storyText">
      Halo! Aku ada pesan rahasia khusus buat kamu nih... Penasaran gak? 🤫
    </div>

    <!-- Tombol Pilihan -->
    <div class="btn-container" id="btnContainer">
      <button class="btn" onclick="nextStep(1)">Penasaran banget!</button>
      <button class="btn btn-secondary" onclick="nextStep(99)">Enggak deh</button>
    </div>
  </div>

  <script>
    /* ============================================================
       ALUR CERITA / STORY STEPS (Bisa Kamu Edit Sesuai Keinginan)
       ============================================================ */
    const storyData = {
      0: {
        text: "Halo! Aku ada pesan rahasia khusus buat kamu nih... Penasaran gak? 🤫",
        buttons: [
          { label: "Penasaran banget!", next: 1 },
          { label: "Enggak deh", next: 99 }
        ]
      },
      1: {
        text: "Hehehe oke! Sebelum aku kasih tau, kamu harus jawab jujur dulu ya. Hari ini seneng gak? 😊",
        buttons: [
          { label: "Seneng dong!", next: 2 },
          { label: "Biasa aja sih", next: 3 }
        ]
      },
      2: {
        text: "Wah baguss deh! Semoga hari-hari kamu seterusnya juga selalu dipenuhi hal bahagia ya! 🌟",
        buttons: [
          { label: "Lanjut pesan rahasia 💌", next: 4 }
        ]
      },
      3: {
        text: "Gapapa, besok pasti harinya bakal lebih seru dan menyenangkan! Semangat terus yaa! ✨",
        buttons: [
          { label: "Lanjut pesan rahasia 💌", next: 4 }
        ]
      },
      4: {
        text: "Sebenernya aku cuma mau nyampein: Terima kasih ya udah selalu ada dan jadi orang yang luar biasa! Have a nice day ❤️",
        buttons: [
          { label: "Mulai dari awal 🔄", next: 0 }
        ]
      },
      99: {
        text: "Yakin gak mau tau? Padahal ini penting banget lho... 🥺",
        buttons: [
          { label: "Oke deh mau tau!", next: 1 },
          { label: "Tetep enggak", next: 0 }
        ]
      }
    };

    /* FUNCTION UNTUK Pindah Halaman/Alur */
    function nextStep(stepIndex) {
      const data = storyData[stepIndex];
      const storyTextEl = document.getElementById("storyText");
      const btnContainerEl = document.getElementById("btnContainer");

      // Animasikan teks
      storyTextEl.classList.remove("fade-in");
      void storyTextEl.offsetWidth; // trigger reflow
      storyTextEl.classList.add("fade-in");

      // Update Teks
      storyTextEl.innerText = data.text;

      // Update Tombol
      btnContainerEl.innerHTML = "";
      data.buttons.forEach((btn, idx) => {
        const buttonEl = document.createElement("button");
        buttonEl.className = idx === 0 ? "btn" : "btn btn-secondary";
        buttonEl.innerText = btn.label;
        buttonEl.onclick = () => nextStep(btn.next);
        btnContainerEl.appendChild(buttonEl);
      });
    }
  </script>
</body>
</html>
