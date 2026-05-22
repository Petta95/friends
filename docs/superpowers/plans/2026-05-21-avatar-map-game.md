# Avatar Map Game Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a single-file browser game with avatar selection, movable avatar on a CSS map, 4 interactive popups, and an end screen.

**Architecture:** Single `index.html` with embedded CSS (`<style>`) and JS (`<script>`). Three screens toggled by a `.active` CSS class. Avatar movement via `requestAnimationFrame`. Popup system renders content dynamically into a single shared modal. No external dependencies.

**Tech Stack:** Vanilla HTML5, CSS3, JavaScript ES6+

---

## File Map

| File | Responsibility |
|------|----------------|
| `index.html` | Everything: structure, styles, game logic |
| `assets/` | User-provided images, GIFs, video (see asset checklist at bottom) |

---

### Task 1: HTML scaffold + screen switching system

**Files:**
- Create: `index.html`

- [ ] **Step 1: Create `index.html` with 3 screen containers and CSS base**

```html
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Il Gioco di Petta 🐾</title>
  <style>
    :root {
      --bg-dark: #0a0a1a;
      --text: #f0f0f0;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background: var(--bg-dark);
      color: var(--text);
      font-family: 'Segoe UI', sans-serif;
      min-height: 100vh;
      overflow: hidden;
    }
    .screen { display: none; width: 100vw; min-height: 100vh; }
    .screen.active { display: flex; }
  </style>
</head>
<body>
  <div id="screen-avatar" class="screen active">
    <p style="color:white;padding:2rem">Screen 1 placeholder</p>
  </div>
  <div id="screen-map" class="screen">
    <p style="color:white;padding:2rem">Screen 2 placeholder</p>
  </div>
  <div id="screen-end" class="screen">
    <p style="color:white;padding:2rem">Screen 3 placeholder</p>
  </div>

  <script>
    function showScreen(id) {
      document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
      document.getElementById(id).classList.add('active');
    }
  </script>
</body>
</html>
```

- [ ] **Step 2: Verify in browser**

Open `index.html`. Expected: dark background, white "Screen 1 placeholder" text. No console errors.

- [ ] **Step 3: Commit**

```bash
git init
git add index.html
git commit -m "feat: html scaffold with 3 screens and screen switcher"
```

---

### Task 2: Screen 1 — Avatar selection HTML + CSS + stars

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace `<div id="screen-avatar">` with full avatar selection markup**

```html
<div id="screen-avatar" class="screen active">
  <div class="stars" id="stars"></div>
  <div class="avatar-container">
    <h1 class="game-title">🐾 Scegli il tuo personaggio</h1>
    <div class="avatar-grid">
      <div class="avatar-card" data-name="angela">
        <div class="avatar-img-wrap">
          <img src="assets/angela.png" alt="Angela" onerror="this.src='';this.parentElement.textContent='🦍'">
        </div>
        <div class="avatar-name">Angela</div>
        <div class="avatar-sub">La più elegante</div>
      </div>
      <div class="avatar-card" data-name="alex">
        <div class="avatar-img-wrap">
          <img src="assets/alex.png" alt="Alex" onerror="this.src='';this.parentElement.textContent='🐷'">
        </div>
        <div class="avatar-name">Alex</div>
        <div class="avatar-sub">Il più profumato</div>
      </div>
      <div class="avatar-card" data-name="iuri">
        <div class="avatar-img-wrap">
          <img src="assets/iuri.png" alt="Iuri" onerror="this.src='';this.parentElement.textContent='🫏'">
        </div>
        <div class="avatar-name">Iuri</div>
        <div class="avatar-sub">Il più testardo</div>
      </div>
      <div class="avatar-card" data-name="alessia">
        <div class="avatar-img-wrap">
          <img src="assets/alessia.png" alt="Alessia" onerror="this.src='';this.parentElement.textContent='🦢'">
        </div>
        <div class="avatar-name">Alessia</div>
        <div class="avatar-sub">La più veloce (in fuga)</div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 2: Add CSS for avatar selection (inside `<style>`, before closing tag)**

```css
/* Stars */
.stars { position: fixed; inset: 0; z-index: 0; pointer-events: none; }
.star {
  position: absolute; width: 2px; height: 2px;
  background: #fff; border-radius: 50%;
  animation: twinkle var(--dur, 2s) ease-in-out infinite;
}
@keyframes twinkle { 0%,100%{opacity:.15} 50%{opacity:1} }

/* Avatar screen layout */
.avatar-container {
  position: relative; z-index: 1;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  width: 100%; padding: 2rem; gap: 2rem;
}
.game-title {
  font-size: 2.2rem; font-weight: 800; text-align: center;
  background: linear-gradient(135deg, #a78bfa, #f472b6);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}

/* Avatar cards */
.avatar-grid { display: grid; grid-template-columns: repeat(2, 260px); gap: 1.5rem; }
.avatar-card {
  background: rgba(255,255,255,0.05);
  border: 2px solid rgba(255,255,255,0.1);
  border-radius: 16px; padding: 1.5rem;
  display: flex; flex-direction: column;
  align-items: center; gap: 0.75rem;
  cursor: pointer;
  transition: transform .2s, border-color .2s, box-shadow .2s;
}
.avatar-card:hover { transform: scale(1.05); }
.avatar-card:nth-child(1):hover { border-color: #f87171; box-shadow: 0 0 24px rgba(248,113,113,0.4); }
.avatar-card:nth-child(2):hover { border-color: #fb923c; box-shadow: 0 0 24px rgba(251,146,60,0.4); }
.avatar-card:nth-child(3):hover { border-color: #4ade80; box-shadow: 0 0 24px rgba(74,222,128,0.4); }
.avatar-card:nth-child(4):hover { border-color: #38bdf8; box-shadow: 0 0 24px rgba(56,189,248,0.4); }
.avatar-img-wrap {
  width: 120px; height: 120px;
  display: flex; align-items: center; justify-content: center;
  font-size: 80px;
}
.avatar-img-wrap img { width: 100%; height: 100%; object-fit: contain; }
.avatar-name { font-size: 1.4rem; font-weight: 700; }
.avatar-sub { font-size: 0.85rem; color: #a0a0c0; font-style: italic; }
```

- [ ] **Step 3: Add star generation JS (inside `<script>`, before closing tag)**

```js
function initStars() {
  const container = document.getElementById('stars');
  for (let i = 0; i < 120; i++) {
    const s = document.createElement('div');
    s.className = 'star';
    s.style.left = Math.random() * 100 + '%';
    s.style.top  = Math.random() * 100 + '%';
    s.style.setProperty('--dur', (1.5 + Math.random() * 3) + 's');
    s.style.animationDelay = (Math.random() * 3) + 's';
    container.appendChild(s);
  }
}
initStars();
```

- [ ] **Step 4: Verify in browser**

Expected: dark background with twinkling white stars, gradient title, 2×2 grid of avatar cards. Each card shows emoji fallback (gorilla/pig/donkey/bird) if PNG not in `assets/`. Hover shows colored glow per card.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: avatar selection screen with stars and card grid"
```

---

### Task 3: Screen 2 — Map HTML + CSS

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace `<div id="screen-map">` with map structure**

```html
<div id="screen-map" class="screen">
  <div class="map-wrapper">
    <div class="map" id="map">
      <div class="road road-h"></div>
      <div class="road road-v"></div>
      <div class="tree" style="left:50px;top:50px">🌳</div>
      <div class="tree" style="left:700px;top:50px">🌲</div>
      <div class="tree" style="left:50px;top:470px">🌲</div>
      <div class="tree" style="left:700px;top:470px">🌳</div>
      <div class="tree" style="left:355px;top:30px">🌿</div>
      <div class="tree" style="left:355px;top:500px">🌿</div>
      <div class="tree" style="left:20px;top:260px">🍀</div>
      <div class="tree" style="left:740px;top:260px">🍀</div>
      <!-- avatar sprite positioned by JS -->
      <img id="avatar-sprite" src="" alt="avatar" style="left:376px;top:256px">
      <!-- hotspots injected by JS -->
    </div>
    <div class="map-hint">⬆⬇⬅➡ oppure WASD per muoverti</div>
  </div>
</div>
```

- [ ] **Step 2: Add map CSS (inside `<style>`)**

```css
/* Map screen */
#screen-map {
  align-items: center; justify-content: center;
  background: #1a1a2e; flex-direction: column; gap: 0.75rem;
}
.map-wrapper { display: flex; flex-direction: column; align-items: center; gap: 0.5rem; }

.map {
  position: relative;
  width: 800px; height: 560px;
  background: #3d7a45;
  border: 3px solid #2d5a32;
  border-radius: 8px;
  overflow: hidden;
  flex-shrink: 0;
}

/* Roads */
.road { position: absolute; background: #6b7280; }
.road-h { left: 0; right: 0; top: 50%; transform: translateY(-50%); height: 60px; }
.road-v { top: 0; bottom: 0; left: 50%; transform: translateX(-50%); width: 60px; }

/* Decorations */
.tree {
  position: absolute;
  font-size: 28px;
  pointer-events: none;
  user-select: none;
}

.map-hint { color: #64748b; font-size: 0.82rem; }

/* Avatar sprite */
#avatar-sprite {
  position: absolute;
  width: 48px; height: 48px;
  object-fit: contain;
  z-index: 10;
  image-rendering: pixelated;
}
```

- [ ] **Step 3: Verify in browser**

Select an avatar → map screen shows green field with a gray road cross, emoji trees in corners and edges. Broken image icon for avatar (normal — src not set yet).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: CSS map layout for screen 2"
```

---

### Task 4: Screen 3 — End screen HTML + CSS

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace `<div id="screen-end">` with end screen**

```html
<div id="screen-end" class="screen">
  <div class="end-container">
    <h1 class="end-title">🎮 Gioco Finito</h1>
    <p class="end-sub">Ora si può tornare a lavorare</p>
    <img src="assets/gif-piange.gif" alt="piange" class="end-gif"
         onerror="this.style.display='none'">
  </div>
</div>
```

- [ ] **Step 2: Add end screen CSS (inside `<style>`)**

```css
#screen-end {
  background: #000;
  align-items: center; justify-content: center;
}
.end-container {
  display: flex; flex-direction: column;
  align-items: center; gap: 1.5rem;
  text-align: center; padding: 2rem;
}
.end-title {
  font-size: 3.5rem; font-weight: 900;
  animation: end-fade 1s ease;
}
.end-sub {
  font-size: 1.5rem; color: #9ca3af;
  animation: end-fade 1s ease 0.3s both;
}
.end-gif {
  max-width: 320px; border-radius: 12px;
  animation: end-fade 1s ease 0.6s both;
}
@keyframes end-fade {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: none; }
}
```

- [ ] **Step 3: Temporarily test end screen by editing `showScreen` call**

In browser console: `showScreen('screen-end')`. Expected: black background, large "🎮 Gioco Finito" title fading in, subtitle, GIF placeholder. Reload to return to avatar selection.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: end screen HTML and CSS"
```

---

### Task 5: Avatar selection logic + state

**Files:**
- Modify: `index.html` (`<script>` section)

- [ ] **Step 1: Add global state object (inside `<script>`, after `showScreen`)**

```js
const state = {
  avatar: null,
  completed: new Set(),
  pos: { x: 376, y: 256 },
  keys: new Set()
};
```

- [ ] **Step 2: Add avatar card click handlers (inside `<script>`, after state)**

```js
document.querySelectorAll('.avatar-card').forEach(card => {
  card.addEventListener('click', () => {
    state.avatar = card.dataset.name;

    // Set avatar sprite to the chosen PNG
    const srcImg = card.querySelector('img');
    const sprite = document.getElementById('avatar-sprite');
    sprite.src = 'assets/' + state.avatar + '.png';
    sprite.alt = state.avatar;
    sprite.onerror = () => {
      // fallback: use a coloured circle if PNG missing
      sprite.style.display = 'none';
      let fb = document.getElementById('avatar-fallback');
      if (!fb) {
        fb = document.createElement('div');
        fb.id = 'avatar-fallback';
        fb.style.cssText = 'position:absolute;width:48px;height:48px;border-radius:50%;background:#a78bfa;display:flex;align-items:center;justify-content:center;font-size:24px;z-index:10;';
        const icons = { angela:'🦍', alex:'🐷', iuri:'🫏', alessia:'🦢' };
        fb.textContent = icons[state.avatar] || '😀';
        document.getElementById('map').appendChild(fb);
      }
    };

    state.pos = { x: 376, y: 256 };
    state.completed = new Set();
    showScreen('screen-map');
    startGame();
  });
});
```

- [ ] **Step 3: Add `startGame` stub (inside `<script>`)**

```js
function startGame() {
  // game loop started here — implemented in Task 6
  console.log('Game started with avatar:', state.avatar);
}
```

- [ ] **Step 4: Verify in browser**

Click any avatar card. Console logs "Game started with avatar: [name]". Map screen shows. Avatar PNG loads (or fallback circle if no assets yet).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: avatar selection click logic and state init"
```

---

### Task 6: Avatar movement

**Files:**
- Modify: `index.html` (`<script>` section)

- [ ] **Step 1: Add keyboard listeners (inside `<script>`, after state)**

```js
document.addEventListener('keydown', e => {
  state.keys.add(e.key);
  if (['ArrowUp','ArrowDown','ArrowLeft','ArrowRight',' '].includes(e.key)) {
    e.preventDefault();
  }
});
document.addEventListener('keyup', e => state.keys.delete(e.key));
```

- [ ] **Step 2: Add `gameLoop` function (inside `<script>`)**

```js
let loopRunning = false;

function gameLoop() {
  if (document.getElementById('screen-map').classList.contains('active')) {
    const spd = 3;
    if (state.keys.has('ArrowLeft')  || state.keys.has('a')) state.pos.x = Math.max(0,   state.pos.x - spd);
    if (state.keys.has('ArrowRight') || state.keys.has('d')) state.pos.x = Math.min(752,  state.pos.x + spd);
    if (state.keys.has('ArrowUp')    || state.keys.has('w')) state.pos.y = Math.max(0,   state.pos.y - spd);
    if (state.keys.has('ArrowDown')  || state.keys.has('s')) state.pos.y = Math.min(512,  state.pos.y + spd);

    const sprite = document.getElementById('avatar-sprite');
    sprite.style.left = state.pos.x + 'px';
    sprite.style.top  = state.pos.y + 'px';

    const fb = document.getElementById('avatar-fallback');
    if (fb) { fb.style.left = state.pos.x + 'px'; fb.style.top = state.pos.y + 'px'; }

    checkProximity();
  }
  requestAnimationFrame(gameLoop);
}
```

- [ ] **Step 3: Replace `startGame` stub with real implementation**

```js
function startGame() {
  const sprite = document.getElementById('avatar-sprite');
  sprite.style.left = state.pos.x + 'px';
  sprite.style.top  = state.pos.y + 'px';
  if (!loopRunning) {
    loopRunning = true;
    gameLoop();
  }
}
```

- [ ] **Step 4: Add `checkProximity` stub (will be replaced in Task 7)**

```js
function checkProximity() { /* implemented in Task 7 */ }
```

- [ ] **Step 5: Verify in browser**

Select an avatar → map screen. Press arrow keys or WASD. Avatar sprite moves around the map and stops at borders (max x=752, max y=512, min 0,0).

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: avatar keyboard movement with requestAnimationFrame"
```

---

### Task 7: Hotspots + proximity detection

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add hotspot data and render function (inside `<script>`, after state)**

```js
const HOTSPOTS = [
  { id: 'paura', icon: '👻', label: 'Paura',  x: 140, y: 110 },
  { id: 'amore', icon: '❤️', label: 'Amore',  x: 600, y: 110 },
  { id: 'sport', icon: '⚽', label: 'Sport',  x: 140, y: 420 },
  { id: 'soldi', icon: '💰', label: 'Soldi',  x: 600, y: 420 }
];

function renderHotspots() {
  const map = document.getElementById('map');
  HOTSPOTS.forEach(hs => {
    const wrap = document.createElement('div');
    wrap.className = 'hotspot';
    wrap.id = 'hs-' + hs.id;
    wrap.style.left = (hs.x - 32) + 'px';
    wrap.style.top  = (hs.y - 32) + 'px';
    wrap.innerHTML = `
      <div class="hs-icon">${hs.icon}</div>
      <div class="hs-label">${hs.label}</div>
      <button class="hs-btn" id="btn-${hs.id}" onclick="openPopup('${hs.id}')">Apri</button>
    `;
    map.appendChild(wrap);
  });
}
renderHotspots();
```

- [ ] **Step 2: Replace `checkProximity` stub with real implementation**

```js
function checkProximity() {
  const cx = state.pos.x + 24;
  const cy = state.pos.y + 24;
  HOTSPOTS.forEach(hs => {
    if (state.completed.has(hs.id)) return;
    const dist = Math.hypot(cx - hs.x, cy - hs.y);
    const btn = document.getElementById('btn-' + hs.id);
    if (dist < 65) btn.classList.add('visible');
    else           btn.classList.remove('visible');
  });
}
```

- [ ] **Step 3: Add hotspot CSS (inside `<style>`)**

```css
.hotspot {
  position: absolute; width: 64px;
  display: flex; flex-direction: column;
  align-items: center; gap: 2px; z-index: 5;
}
.hs-icon {
  font-size: 40px;
  animation: hs-pulse 2s ease-in-out infinite;
  filter: drop-shadow(0 0 6px rgba(255,255,255,0.4));
}
@keyframes hs-pulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.15)} }
.hs-label {
  font-size: 0.7rem; color: #fff; font-weight: 600;
  text-shadow: 0 1px 3px #000; white-space: nowrap;
}
.hs-btn {
  display: none; margin-top: 4px;
  padding: 2px 8px; background: #7c3aed;
  color: #fff; border: none; border-radius: 6px;
  font-size: 0.7rem; cursor: pointer; white-space: nowrap;
}
.hs-btn.visible {
  display: block;
  animation: btn-pop .2s ease;
}
@keyframes btn-pop { from{transform:scale(.5);opacity:0} to{transform:scale(1);opacity:1} }
.hotspot.done .hs-icon { filter: grayscale(1) opacity(.45); animation: none; }
.hotspot.done .hs-label::after { content: ' ✓'; color: #4ade80; }
```

- [ ] **Step 4: Add `openPopup` stub (inside `<script>` — will be replaced in Task 8)**

```js
function openPopup(id) {
  alert('Popup ' + id + ' — stub, verrà implementato nei task successivi');
}
```

- [ ] **Step 5: Verify in browser**

Select avatar → enter map. Move toward each corner. At ~65px from each hotspot icon (👻 top-left, ❤️ top-right, ⚽ bottom-left, 💰 bottom-right) an "Apri" button pops in. Moving away hides it. Clicking shows the stub alert.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: hotspot icons with proximity-triggered open button"
```

---

### Task 8: Popup system base

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add popup overlay HTML (inside `<body>`, after `<div id="screen-end">`)**

```html
<!-- Shared popup overlay -->
<div id="popup-overlay" class="popup-overlay" style="display:none">
  <div class="popup-box" id="popup-box"></div>
</div>
```

- [ ] **Step 2: Add popup CSS (inside `<style>`)**

```css
.popup-overlay {
  position: fixed; inset: 0;
  background: rgba(0,0,0,0.82);
  z-index: 100;
  display: flex; align-items: center; justify-content: center;
}
.popup-box {
  background: #1e1e3a; border-radius: 16px;
  padding: 2rem; max-width: 580px; width: 92%;
  max-height: 90vh; overflow-y: auto;
  display: flex; flex-direction: column;
  align-items: center; gap: 1.25rem;
  text-align: center;
  box-shadow: 0 8px 40px rgba(0,0,0,0.7);
}
.popup-box h2 { font-size: 1.6rem; font-weight: 800; }
.popup-box p  { font-size: 1.05rem; line-height: 1.6; }
.popup-box img { max-width: 100%; border-radius: 8px; max-height: 280px; object-fit: contain; }
.popup-box video { max-width: 100%; border-radius: 8px; }
.popup-btn {
  padding: 0.6rem 1.6rem; border: none;
  border-radius: 8px; font-weight: 700;
  cursor: pointer; transition: transform .1s;
}
.popup-btn:hover { transform: scale(1.05); }
.popup-btn.big   { background: #4ade80; color: #000; font-size: 1.1rem; padding: 0.75rem 2rem; }
.popup-btn.small { background: #6b7280; color: #ccc; }
.popup-btn.close-btn { background: #7c3aed; color: #fff; font-size: 1rem; padding: 0.6rem 1.6rem; }
```

- [ ] **Step 3: Replace `openPopup` stub with real system (inside `<script>`)**

```js
function openPopup(id) {
  const overlay = document.getElementById('popup-overlay');
  overlay.style.display = 'flex';
  renderPopup(id);
}

function closePopup(id, completed = false) {
  document.getElementById('popup-overlay').style.display = 'none';
  document.getElementById('popup-box').innerHTML = '';
  if (completed) markDone(id);
}

function markDone(id) {
  state.completed.add(id);
  const hs = document.getElementById('hs-' + id);
  if (hs) hs.classList.add('done');
  const btn = document.getElementById('btn-' + id);
  if (btn) btn.classList.remove('visible');
  if (state.completed.size === 4) {
    setTimeout(() => showScreen('screen-end'), 600);
  }
}

function renderPopup(id) {
  const box = document.getElementById('popup-box');
  if (id === 'paura') { renderPaura(box, 1); return; }
  if (id === 'amore') { renderAmore(box);    return; }
  if (id === 'sport') { renderSport(box);    return; }
  if (id === 'soldi') { renderSoldi(box);    return; }
}

// Stubs — replaced in Tasks 9-12
function renderPaura(box, step) {
  box.innerHTML = `<h2>👻 Paura stub</h2><button class="popup-btn close-btn" onclick="closePopup('paura',true)">Chiudi</button>`;
}
function renderAmore(box) {
  box.innerHTML = `<h2>❤️ Amore stub</h2><button class="popup-btn close-btn" onclick="closePopup('amore',true)">Chiudi</button>`;
}
function renderSport(box) {
  box.innerHTML = `<h2>⚽ Sport stub</h2><button class="popup-btn close-btn" onclick="closePopup('sport',true)">Chiudi</button>`;
}
function renderSoldi(box) {
  box.innerHTML = `<h2>💰 Soldi stub</h2><button class="popup-btn close-btn" onclick="closePopup('soldi',true)">Chiudi</button>`;
}
```

- [ ] **Step 4: Verify in browser**

Go near each hotspot, click "Apri". A dark modal appears. Clicking "Chiudi" marks the hotspot done (icon greys out with ✓). After closing all 4 stubs → end screen appears automatically after 600ms.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: popup overlay system with open/close/markDone and completion flow"
```

---

### Task 9: Popup Paura — 3-step flow

**Files:**
- Modify: `index.html` (`<script>` section)

- [ ] **Step 1: Replace `renderPaura` stub with full 3-step implementation**

```js
function renderPaura(box, step) {
  box.style.background = '#2d0000';
  if (step === 1) {
    box.innerHTML = `
      <h2>👻 Attenzione!</h2>
      <p style="font-size:1.3rem;font-weight:700">È molto spaventoso,<br>sei sicuro?</p>
      <div style="display:flex;gap:1.5rem;align-items:center;flex-wrap:wrap;justify-content:center">
        <button class="popup-btn big" onclick="closePopup('paura',false)">NO, grazie!</button>
        <button class="popup-btn small" style="font-size:0.7rem;padding:4px 10px"
                onclick="renderPaura(document.getElementById('popup-box'),2)">sì</button>
      </div>`;
  } else if (step === 2) {
    box.innerHTML = `
      <h2>⚠️ Attenzione!</h2>
      <p style="font-size:1.1rem">Le immagini seguenti sono riservate ai <strong>18+</strong></p>
      <div style="display:flex;gap:1.5rem;align-items:center;flex-wrap:wrap;justify-content:center">
        <button class="popup-btn big" onclick="closePopup('paura',false)">NO, torno indietro!</button>
        <button class="popup-btn small" style="font-size:0.5rem;padding:2px 6px;opacity:0.7"
                onclick="renderPaura(document.getElementById('popup-box'),3)">ok sì</button>
      </div>`;
  } else {
    box.innerHTML = `
      <h2>😱 Eccole!</h2>
      <img src="assets/piedi.jpg" alt="piedi" onerror="this.style.display='none'">
      <img src="assets/gif-urlo.gif" alt="urlo" style="max-height:180px" onerror="this.style.display='none'">
      <button class="popup-btn close-btn" onclick="closePopup('paura',true)">CHIUDI (se riesci)</button>`;
  }
}
```

- [ ] **Step 2: Verify in browser**

Move near 👻, click "Apri".
- **Step 1:** Big green "NO, grazie!" and tiny grey "sì". Click NO → popup closes, hotspot NOT done (can re-enter and try again). Click tiny "sì" → step 2.
- **Step 2:** Big "NO, torno indietro!" and even tinier semi-transparent "ok sì". Click "ok sì" → step 3.
- **Step 3:** `assets/piedi.jpg` + `assets/gif-urlo.gif` visible (hidden if files missing). Click "CHIUDI" → hotspot marked done ✓.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: popup Paura 3-step flow with progressively smaller sì buttons"
```

---

### Task 10: Popup Amore — celebrity grid

**Files:**
- Modify: `index.html` (`<script>` section)

- [ ] **Step 1: Replace `renderAmore` stub with full implementation**

```js
function renderAmore(box) {
  box.style.background = '#2d0020';
  box.innerHTML = `
    <h2>❤️ Decidi la fidanzata di Petta!</h2>
    <p style="color:#f9a8d4;font-size:0.95rem">Scegli con saggezza 💕</p>
    <div class="celeb-grid">
      ${[1,2,3,4,5].map(n => `
        <div class="celeb-card" onclick="selectCeleb(this)">
          <img src="assets/celeb${n}.jpg" alt="Celebrity ${n}"
               onerror="this.src='';this.parentElement.innerHTML='⭐'">
        </div>`).join('')}
    </div>
    <div id="amore-result" style="display:none;flex-direction:column;align-items:center;gap:1rem">
      <p style="font-size:1.4rem;font-weight:800;color:#f472b6">Buona scelta! 😏</p>
      <img src="assets/gif-malizioso.gif" alt="malizioso" style="max-height:180px"
           onerror="this.style.display='none'">
      <button class="popup-btn close-btn" onclick="closePopup('amore',true)">Conferma la scelta 💘</button>
    </div>`;
}

function selectCeleb(card) {
  document.querySelectorAll('.celeb-card').forEach(c => c.classList.remove('selected'));
  card.classList.add('selected');
  const result = document.getElementById('amore-result');
  result.style.display = 'flex';
}
```

- [ ] **Step 2: Add celebrity grid CSS (inside `<style>`)**

```css
.celeb-grid {
  display: grid;
  grid-template-columns: repeat(5, 90px);
  gap: 0.75rem;
  justify-content: center;
}
.celeb-card {
  width: 90px; height: 90px;
  border-radius: 10px; overflow: hidden;
  cursor: pointer;
  border: 3px solid transparent;
  transition: border-color .2s, transform .2s;
  display: flex; align-items: center; justify-content: center;
  background: #3d1a3d; font-size: 2.5rem;
}
.celeb-card img { width: 100%; height: 100%; object-fit: cover; }
.celeb-card:hover { transform: scale(1.08); border-color: #f472b6; }
.celeb-card.selected {
  border-color: #f472b6;
  box-shadow: 0 0 16px rgba(244,114,182,0.5);
}
```

- [ ] **Step 3: Verify in browser**

Move near ❤️, click "Apri".
- 5 celebrity card slots visible (⭐ emoji if images missing).
- Click any card → it gets a pink border, "Buona scelta! 😏" + GIF appears below.
- Click a different card → selection moves.
- Click "Conferma la scelta" → popup closes, ❤️ marked done ✓.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: popup Amore with celebrity grid and selection flow"
```

---

### Task 11: Popup Sport + Popup Soldi

**Files:**
- Modify: `index.html` (`<script>` section)

- [ ] **Step 1: Replace `renderSport` stub**

```js
function renderSport(box) {
  box.style.background = '#1a0d00';
  box.innerHTML = `
    <h2>⚽ La Mossa Leggendaria</h2>
    <video controls style="width:100%;max-height:260px;border-radius:8px">
      <source src="assets/video-sport.mp4" type="video/mp4">
      <p style="color:#fb923c;padding:1rem">Video non trovato — aggiungi <code>assets/video-sport.mp4</code></p>
    </video>
    <p style="font-size:1.15rem;font-weight:700;color:#fb923c">
      Alex e Iuri non ci sono riusciti 😂
    </p>
    <img src="assets/gif-atleta.gif" alt="atleta" style="max-height:160px"
         onerror="this.style.display='none'">
    <button class="popup-btn close-btn" onclick="closePopup('sport',true)">Chapeau 👏</button>`;
}
```

- [ ] **Step 2: Replace `renderSoldi` stub**

```js
function renderSoldi(box) {
  box.style.background = '#001a00';
  box.innerHTML = `
    <h2>💰 Popup Soldi</h2>
    <p style="font-size:2rem;font-weight:900;color:#4ade80;line-height:1.3">
      Ti piacerebbe<br>scemoooooo 🤑
    </p>
    <img src="assets/gif-povero.gif" alt="povero" style="max-height:220px"
         onerror="this.style.display='none'">
    <button class="popup-btn close-btn" onclick="closePopup('soldi',true)">Chiudi 😢</button>`;
}
```

- [ ] **Step 3: Verify in browser**

**Sport:** Move near ⚽, click "Apri". Dark brown popup with video player (or error note if file missing). Text "Alex e Iuri non ci sono riusciti 😂". Athlete GIF. Click "Chapeau" → ⚽ done ✓.

**Soldi:** Move near 💰, click "Apri". Dark green popup with large "Ti piacerebbe scemoooooo 🤑". Poor GIF. Click "Chiudi" → 💰 done ✓.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: popup Sport and popup Soldi"
```

---

### Task 12: Full game flow verification + polish

**Files:**
- Modify: `index.html` (small tweaks only if needed)

- [ ] **Step 1: Full playthrough test**

Open `index.html` in browser. Complete this sequence:
1. See star screen → click Angela → enter map ✓
2. Move to 👻 (top-left): click "Apri" → click NO → popup closes, hotspot NOT done. Re-enter, click "Apri" → sì → sì → CHIUDI → 👻 ✓ greyed.
3. Move to ❤️ (top-right): click "Apri" → select celebrity → Conferma → ❤️ ✓ greyed.
4. Move to ⚽ (bottom-left): click "Apri" → Chapeau → ⚽ ✓ greyed.
5. Move to 💰 (bottom-right): click "Apri" → Chiudi → 💰 ✓ greyed.
6. 600ms after last popup → **end screen** fades in with "🎮 Gioco Finito / Ora si può tornare a lavorare" + GIF.

- [ ] **Step 2: Repeat playthrough with each of the 4 avatars**

Expected: same behavior for Alex, Iuri, Alessia. Avatar sprite changes per selection.

- [ ] **Step 3: Final commit**

```bash
git add index.html
git commit -m "feat: complete game — all screens, popups, and end flow verified"
```

---

## Asset Checklist

Place these files in `assets/` before showing to others:

| File | Used in |
|------|---------|
| `angela.png` | avatar gorilla |
| `alex.png` | avatar maiale |
| `iuri.png` | avatar asino |
| `alessia.png` | avatar struzzo |
| `piedi.jpg` | popup Paura step 3 |
| `celeb1.jpg` – `celeb5.jpg` | popup Amore celebrity grid |
| `video-sport.mp4` | popup Sport video |
| `gif-urlo.gif` | popup Paura step 3 |
| `gif-malizioso.gif` | popup Amore |
| `gif-atleta.gif` | popup Sport |
| `gif-povero.gif` | popup Soldi |
| `gif-piange.gif` | schermata finale |

All assets have graceful fallbacks: missing images are hidden, missing video shows an error message, avatar PNG falls back to an emoji circle.
