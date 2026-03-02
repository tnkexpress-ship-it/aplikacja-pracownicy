# aplikacja-pracownicy
<!DOCTYPE html>
<html lang="pl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Aplikacja dla pracowników</title>
<style>
  body { font-family: Arial, sans-serif; margin: 20px; }
  .tabs { display: flex; margin-bottom: 20px; }
  .tab { padding: 10px 20px; margin-right: 5px; cursor: pointer; background: #ccc; border-radius: 5px 5px 0 0; }
  .tab.active { background: #3b82f6; color: white; }
  .tab-content { border: 1px solid #ccc; padding: 20px; border-radius: 0 5px 5px 5px; }
  input[type="number"], input[type="file"], textarea { padding: 5px; margin: 5px 0; width: 100%; }
  button { padding: 10px 20px; margin-top: 10px; cursor: pointer; }
  .photo-item { margin: 10px 0; }
  .photo-item img { max-width: 200px; display: block; margin-bottom: 5px; }
</style>
</head>
<body>

<h1>Aplikacja dla pracowników</h1>

<div class="tabs">
  <div class="tab active" onclick="showTab('points')">Punkty i wynagrodzenie</div>
  <div class="tab" onclick="showTab('photos')">Zdjęcia płynów</div>
</div>

<div class="tab-content" id="points">
  <h2>Punkty i wynagrodzenie</h2>
  <label>Dodaj punkty wykonane dzisiaj:</label>
  <input type="number" id="pointsInput" min="0" placeholder="Ilość punktów">
  <button onclick="addPoints()">Dodaj punkty</button>

  <h3>Podsumowanie 2 tygodni:</h3>
  <p>Suma punktów: <span id="totalPoints">0</span></p>
  <p>Wynagrodzenie (3 zł za punkt): <span id="totalSalary">0</span> zł</p>
</div>

<div class="tab-content" id="photos" style="display:none;">
  <h2>Zdjęcia płynów w samochodzie</h2>
  <input type="file" id="photoInput" accept="image/*">
  <textarea id="photoDesc" placeholder="Opis (opcjonalnie)"></textarea>
  <button onclick="addPhoto()">Dodaj zdjęcie</button>

  <div id="photoGallery"></div>
</div>

<script>
  // --- Zakładki ---
  function showTab(tabName) {
    document.getElementById('points').style.display = (tabName === 'points') ? 'block' : 'none';
    document.getElementById('photos').style.display = (tabName === 'photos') ? 'block' : 'none';
    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    if(tabName === 'points') document.querySelector('.tab:nth-child(1)').classList.add('active');
    else document.querySelector('.tab:nth-child(2)').classList.add('active');
  }

  // --- Punkty i wynagrodzenie ---
  const points = [];
  const rate = 3; // 3 zł za punkt

  function addPoints() {
    const input = document.getElementById('pointsInput');
    const value = parseInt(input.value);
    if (isNaN(value) || value < 0) { alert("Wprowadź poprawną liczbę punktów"); return; }
    points.push({ date: new Date(), value: value });
    updateSummary();
    input.value = '';
  }

  function updateSummary() {
    const now = new Date();
    const twoWeeksAgo = new Date(now.getTime() - 14*24*60*60*1000);
    const recentPoints = points.filter(p => p.date >= twoWeeksAgo);
    const total = recentPoints.reduce((sum,p) => sum + p.value, 0);
    document.getElementById('totalPoints').innerText = total;
    document.getElementById('totalSalary').innerText = total * rate;
  }

  // --- Zdjęcia ---
  const photos = [];

  function addPhoto() {
    const fileInput = document.getElementById('photoInput');
    const desc = document.getElementById('photoDesc').value;
    if (!fileInput.files[0]) { alert("Wybierz zdjęcie"); return; }

    const reader = new FileReader();
    reader.onload = function(e) {
      photos.push({ src: e.target.result, desc: desc });
      renderPhotos();
      fileInput.value = '';
      document.getElementById('photoDesc').value = '';
    }
    reader.readAsDataURL(fileInput.files[0]);
  }

  function renderPhotos() {
    const gallery = document.getElementById('photoGallery');
    gallery.innerHTML = '';
    photos.forEach((p,i) => {
      const div = document.createElement('div');
      div.classList.add('photo-item');
      div.innerHTML = `<img src="${p.src}" alt="Zdjęcie ${i+1}"><p>${p.desc}</p>`;
      gallery.appendChild(div);
    });
  }
</script>

</body>
</html>
// firebase-config.js
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.6.0/firebase-app.js";
import { getAuth } from "https://www.gstatic.com/firebasejs/10.6.0/firebase-auth.js";
import { getFirestore } from "https://www.gstatic.com/firebasejs/10.6.0/firebase-firestore.js";
import { getStorage } from "https://www.gstatic.com/firebasejs/10.6.0/firebase-storage.js";

const firebaseConfig = {
  apiKey: "TWÓJ_API_KEY",
  authDomain: "TWÓJ_PROJEKT.firebaseapp.com",
  projectId: "TWÓJ_PROJEKT",
  storageBucket: "TWÓJ_PROJEKT.appspot.com",
  messagingSenderId: "TWÓJ_ID",
  appId: "TWÓJ_APP_ID"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const db = getFirestore(app);
export const storage = getStorage(app);
{
  "userId": "ID_pracownika",
  "url": "link_do_storage",
  "description": "Opis zdjęcia",
  "timestamp": "data dodania"
}
{
  "email": "pracownik@firma.com",
  "role": "employee", // lub "admin"
  "points": 0,
  "salary": 0
}
/app
 ├─ index.html         // strona logowania
 ├─ employee.html      // panel pracownika
 ├─ admin.html         // panel administratora
 ├─ main.js            // wspólny JS dla logowania i paneli
 ├─ firebase-config.js // konfiguracja Firebase
 └─ style.css          // style dla wszystkich stron
