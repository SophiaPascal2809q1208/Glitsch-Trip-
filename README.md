# Glitsch-Trip-
Glitsch Trip 
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#2c3e50">
    <title>GLITCH TRIP</title>
    
    <link rel="manifest" href="data:application/manifest+json,{%22name%22:%22Glitch%20Trip%22,%22short_name%22:%22GlitchTrip%22,%22start_url%22:%22.%22,%22display%22:%22standalone%22,%22background_color%22:%22%232c3e50%22,%22theme_color%22:%22%232c3e50%22,%22orientation%22:%22landscape%22}">

    <style>
        :root {
            --pixel-blue: #3498db;
            --pixel-green: #2ecc71;
            --pixel-red: #e74c3c;
            --pixel-gold: #f1c40f;
            --pixel-dark: #2c3e50;
        }

        body {
            margin: 0;
            background: #1a1a1a;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            color: white;
            font-family: 'Courier New', Courier, monospace;
            overflow: hidden;
            user-select: none;
            -webkit-user-select: none;
        }

        .overlay-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(#5cacee, #2ecc71);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            text-align: center;
        }

        #levelSelectScreen { background: var(--pixel-dark); display: none; }
        #pauseScreen, #loseScreen, #winScreen { background: rgba(0, 0, 0, 0.9); display: none; }

        h1 {
            font-size: 60px;
            margin-bottom: 40px;
            text-shadow: 4px 4px 0px var(--pixel-dark);
            color: var(--pixel-gold);
        }

        .menu-btn {
            background: var(--pixel-blue);
            padding: 15px 40px;
            margin: 10px;
            font-size: 24px;
            border: 4px solid white;
            color: white;
            cursor: pointer;
            box-shadow: 0 6px 0 #2980b9;
            min-width: 250px;
            font-weight: bold;
        }

        .menu-btn:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 #2980b9;
        }

        .level-grid { 
            display: grid; 
            grid-template-columns: repeat(5, 1fr); 
            gap: 15px; 
            max-width: 90%;
        }
        .level-card {
            width: 70px; height: 70px; background: var(--pixel-blue); border: 4px solid white;
            display: flex; justify-content: center; align-items: center; font-size: 26px; position: relative; cursor: pointer;
        }
        .level-card.locked { background: #7f8c8d; cursor: not-allowed; opacity: 0.6; }
        .level-card.locked::after { content: '🔒'; font-size: 16px; position: absolute; bottom: 3px; right: 3px; }

        #pauseToggle {
            position: absolute; top: 20px; right: 20px; width: 50px; height: 50px;
            background: var(--pixel-red); border: 3px solid white; z-index: 50;
            display: none; justify-content: center; align-items: center; font-size: 24px; font-weight: bold; cursor: pointer;
        }

        #canvasContainer { position: relative; display: none; }
        canvas { border: 6px solid white; background: #5cacee; max-width: 100vw; max-height: 60vh; }

        #controls {
            display: none; justify-content: space-between; width: 100%; max-width: 800px; padding: 10px; box-sizing: border-box;
        }

        .touch-btn {
            width: 70px; height: 70px; background: rgba(255, 255, 255, 0.3); border: 3px solid white;
            border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 30px; touch-action: none;
        }
        .dir-buttons { display: flex; gap: 20px; }
    </style>
</head>
<body>

    <div id="homeMenu" class="overlay-screen" style="display: flex;">
        <h1>GLITCH TRIP</h1>
        <div class="menu-btn" id="playBtn">PLAY</div>
        <div class="menu-btn" id="levelsBtn" style="background:var(--pixel-gold); box-shadow:0 6px 0 #d35400;">LEVELS</div>
    </div>

    <div id="levelSelectScreen" class="overlay-screen">
        <h1>LEVELS</h1>
        <div class="level-grid" id="levelGrid"></div>
        <div class="menu-btn" id="backToHomeBtn" style="margin-top:40px; background:var(--pixel-red);">BACK</div>
    </div>

    <div id="pauseScreen" class="overlay-screen">
        <h1>PAUSED</h1>
        <div class="menu-btn" id="resumeBtn">RESUME</div>
        <div class="menu-btn" id="pauseHomeBtn" style="background:var(--pixel-red);">HOME</div>
    </div>

    <div id="loseScreen" class="overlay-screen">
        <h1 style="color:var(--pixel-red)">YOU LOST! 💀</h1>
        <div class="menu-btn" id="retryBtn">TRY AGAIN</div>
        <div class="menu-btn" id="loseHomeBtn" style="background:var(--pixel-red);">HOME</div>
    </div>

    <div id="winScreen" class="overlay-screen">
        <h1 style="color:var(--pixel-gold)">VICTORY! 🏆</h1>
        <div class="menu-btn" id="resetBtn">MENU</div>
    </div>

    <div id="pauseToggle">||</div>
    <div id="gameHud" style="display:none; font-size: 24px; margin-bottom: 5px; font-weight: bold;">Level: <span id="levelDisplay">1</span></div>
    
    <div id="canvasContainer">
        <canvas id="gameCanvas" width="800" height="600"></canvas>
    </div>

    <div id="controls">
        <div class="dir-buttons">
            <div class="touch-btn" id="btnLeft">←</div>
            <div class="touch-btn" id="btnRight">→</div>
        </div>
        <div class="touch-btn" id="btnJump">↑</div>
    </div>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let gameState = 'HOME'; 
let currentLevel = 0;

// APP-SPEICHERUNG: Lädt den Spielstand lokal vom jeweiligen Gerät
let unlockedLevels = parseInt(localStorage.getItem('glitchTripProgressApp')) || 1;
const totalLevels = 10;
let level8Trolled = false;

const player = {
    x: 50, y: 500, width: 32, height: 48, speed: 6,
    velX: 0, velY: 0, jumping: false, grounded: false, facing: "right",
    coyoteCounter: 0 
};
const gravity = 0.5;
const friction = 0.8;
const keys = {};
let touchLeft = false, touchRight = false, touchJumpIntent = false;
let animationFrameId = null;

let levels = [];
let clouds = [
    { x: 100, y: 80, size: 40 },
    { x: 400, y: 50, size: 60 },
    { x: 700, y: 90, size: 50 }
];

function initLevels() {
    levels = [
        // LEVEL 1
        {
            bgColor: "#5cacee", start: { x: 50, y: 500 }, goal: { x: 720, y: 150, width: 35, height: 55 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 800, height: 40, color: "#2ecc71" },
                { type: "plat", x: 200, y: 450, width: 120, height: 20, color: "#7f8c8d" },
                { type: "fake_hole", x: 380, y: 350, width: 140, height: 20, color: "#7f8c8d", triggerX: 390, active: true },
                { type: "trap", x: 380, y: 545, width: 140, height: 15, color: "#e74c3c" }, 
                { type: "rescue_plat", x: 540, y: 350, width: 90, height: 20, color: "#9b59b6", visible: false, minY: 240, maxY: 440, dir: 1, speed: 3 },
                { type: "plat", x: 670, y: 240, width: 120, height: 20, color: "#7f8c8d" }
            ]
        },
        // LEVEL 2
        {
            bgColor: "#34495e", start: { x: 50, y: 500 }, goal: { x: 720, y: 500, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 800, height: 40, color: "#27ae60" },
                { type: "falling_wall", x: 420, y: -250, width: 70, height: 450, color: "#e74c3c", triggerX: 310, speed: 16, state: "idle", timer: 0 },
                { type: "plat", x: 230, y: 440, width: 100, height: 20, color: "#7f8c8d" }
            ]
        },
        // LEVEL 3
        {
            bgColor: "#2c3e50", start: { x: 50, y: 500 }, goal: { x: 720, y: 200, width: 35, height: 55, triggered: false },
            objects: [
                { type: "plat", x: 0, y: 560, width: 250, height: 40, color: "#1e824c" },
                { type: "trap", x: 250, y: 550, width: 350, height: 15, color: "#e74c3c" }, 
                { type: "plat", x: 600, y: 560, width: 200, height: 40, color: "#1e824c" },
                { type: "moving_plat", x: 235, y: 420, width: 140, height: 20, color: "#f39c12", minX: 235, maxX: 460, dir: 1, speed: 3 },
                { type: "plat", x: 620, y: 290, width: 120, height: 20, color: "#7f8c8d" },
                { type: "surprise_spike", x: 340, y: 600, width: 50, height: 120, color: "#e74c3c", active: false, triggerX: 450 }
            ]
        },
        // LEVEL 4
        {
            bgColor: "#8e44ad", start: { x: 50, y: 500 }, goal: { x: 720, y: 500, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 800, height: 40, color: "#2ecc71" },
                { type: "ceiling_trap", x: 300, y: -100, width: 200, height: 40, color: "#e74c3c", state: "idle", speed: 12 }
            ]
        },
        // LEVEL 5
        {
            bgColor: "#16a085", start: { x: 50, y: 500 }, goal: { x: 720, y: 500, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 150, height: 40, color: "#2ecc71" },
                { type: "trap", x: 150, y: 575, width: 500, height: 15, color: "#e74c3c" },
                { type: "plat", x: 650, y: 560, width: 150, height: 40, color: "#2ecc71" },
                { type: "invisible_bridge", x: 220, y: 450, width: 100, height: 20, color: "#7f8c8d", visible: false, triggerX: 130 },
                { type: "invisible_bridge", x: 420, y: 450, width: 100, height: 20, color: "#7f8c8d", visible: false, triggerX: 330 }
            ]
        },
        // LEVEL 6
        {
            bgColor: "#d35400", start: { x: 50, y: 500 }, goal: { x: 700, y: 410, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 200, height: 40, color: "#2ecc71" },
                { type: "breaking_ground", x: 200, y: 560, width: 300, height: 40, color: "#e67e22", state: "idle", speed: 0 },
                { type: "trap", x: 200, y: 590, width: 300, height: 10, color: "#e74c3c" },
                { type: "plat", x: 500, y: 470, width: 300, height: 130, color: "#2ecc71" }
            ]
        },
        // LEVEL 7
        {
            bgColor: "#2c3e50", start: { x: 50, y: 500 }, goal: { x: 720, y: 500, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 800, height: 40, color: "#2ecc71" },
                { type: "teleporter", x: 380, y: 500, width: 60, height: 60, color: "#9b59b6", active: true }
            ]
        },
        // LEVEL 8
        {
            bgColor: "#7f8c8d", start: { x: 50, y: 500 }, 
            goal: level8Trolled ? { x: 60, y: 350, width: 35, height: 60, isFake: false } : { x: 720, y: 500, width: 35, height: 60, isFake: true },
            objects: [
                { type: "plat", x: 0, y: 560, width: 800, height: 40, color: "#2ecc71" },
                { type: "plat", x: 30, y: 410, width: 100, height: 20, color: "#7f8c8d" },
                { type: level8Trolled ? "trap" : "plat", x: 715, y: 500, width: 45, height: 60, color: level8Trolled ? "#e74c3c" : "rgba(0,0,0,0)" }
            ]
        },
        // LEVEL 9
        {
            bgColor: "#c0392b", start: { x: 50, y: 500 }, goal: { x: 720, y: 150, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 200, height: 40, color: "#2ecc71" },
                { type: "rocket_plat", x: 300, y: 500, width: 120, height: 20, color: "#f1c40f", state: "idle", speed: 0 },
                { type: "trap", x: 200, y: 580, width: 600, height: 20, color: "#e74c3c" },
                { type: "plat", x: 650, y: 250, width: 150, height: 350, color: "#2ecc71" }
            ]
        },
        // LEVEL 10
        {
            bgColor: "#2c3e50", start: { x: 50, y: 500 }, goal: { x: 730, y: 440, width: 35, height: 60 },
            objects: [
                { type: "plat", x: 0, y: 560, width: 150, height: 40, color: "#27ae60" },
                { type: "moving_plat", x: 180, y: 450, width: 120, height: 20, color: "#f39c12", minX: 180, maxX: 400, dir: 1, speed: 4 },
                { type: "falling_wall", x: 500, y: -200, width: 50, height: 400, color: "#e74c3c", triggerX: 350, speed: 14, state: "idle", timer: 0 },
                { type: "trap", x: 150, y: 570, width: 500, height: 30, color: "#e74c3c" },
                { type: "plat", x: 650, y: 500, width: 150, height: 100, color: "#27ae60" },
                { type: "final_surprise", x: 720, y: 400, width: 50, height: 10, color: "#e74c3c", active: false }
            ]
        }
    ];
}

function showScreen(screenId) {
    document.querySelectorAll('.overlay-screen, #canvasContainer, #controls, #pauseToggle, #gameHud').forEach(el => el.style.display = 'none');
    
    if(screenId === 'GAME') {
        document.getElementById('canvasContainer').style.display = 'block';
        document.getElementById('controls').style.display = 'flex';
        document.getElementById('pauseToggle').style.display = 'flex';
        document.getElementById('gameHud').style.display = 'block';
        gameState = 'PLAYING';
    } else {
        document.getElementById(screenId).style.display = 'flex';
        if (screenId === 'homeMenu') gameState = 'HOME';
        else if (screenId === 'levelSelectScreen') gameState = 'LEVEL_SELECT';
        else if (screenId === 'pauseScreen') gameState = 'PAUSED';
        else if (screenId === 'loseScreen') gameState = 'LOSE';
        else if (screenId === 'winScreen') gameState = 'WIN';
    }
}

function startLevel(idx) {
    currentLevel = idx;
    if (idx !== 7) level8Trolled = false; 
    document.getElementById('levelDisplay').innerText = idx + 1;
    resetPlayer();
}

function resetPlayer() {
    if (animationFrameId) {
        cancelAnimationFrame(animationFrameId);
        animationFrameId = null;
    }

    touchLeft = false; 
    touchRight = false; 
    touchJumpIntent = false;
    player.velX = 0; 
    player.velY = 0;
    player.jumping = false; 
    player.grounded = false;
    player.coyoteCounter = 0;

    initLevels();
    
    player.x = 50;
    player.y = 450; 
    
    showScreen('GAME'); 
    update(); 
}

function updateLevelGrid() {
    const grid = document.getElementById('levelGrid');
    grid.innerHTML = '';
    for(let i = 0; i < totalLevels; i++) {
        const card = document.createElement('div');
        card.className = 'level-card' + (i + 1 > unlockedLevels ? ' locked' : '');
        if(i + 1 <= unlockedLevels) {
            card.innerText = i + 1;
            card.onclick = () => startLevel(i);
        }
        grid.appendChild(card);
    }
}

document.getElementById('playBtn').onclick = () => startLevel(unlockedLevels - 1);
document.getElementById('levelsBtn').onclick = () => { updateLevelGrid(); showScreen('levelSelectScreen'); };
document.getElementById('backToHomeBtn').onclick = () => showScreen('homeMenu');
document.getElementById('pauseToggle').onclick = () => showScreen('pauseScreen');
document.getElementById('resumeBtn').onclick = () => showScreen('GAME');
document.getElementById('pauseHomeBtn').onclick = () => showScreen('homeMenu');
document.getElementById('retryBtn').onclick = () => resetPlayer(); 
document.getElementById('loseHomeBtn').onclick = () => showScreen('homeMenu');
document.getElementById('resetBtn').onclick = () => showScreen('homeMenu');

const bind = (id, s, e) => {
    const el = document.getElementById(id);
    el.addEventListener('touchstart', (ev) => { ev.preventDefault(); s(); }, {passive:false});
    el.addEventListener('touchend', (ev) => { ev.preventDefault(); e(); }, {passive:false});
};
bind('btnLeft', () => { if(gameState==='PLAYING') { touchLeft = true; player.facing = "left"; } }, () => touchLeft = false);
bind('btnRight', () => { if(gameState==='PLAYING') { touchRight = true; player.facing = "right"; } }, () => touchRight = false);

bind('btnJump', () => { if(gameState === 'PLAYING') touchJumpIntent = true; }, () => { touchJumpIntent = false; });

window.addEventListener("keydown", e => { keys[e.code] = true; });
window.addEventListener("keyup", e => { keys[e.code] = false; });

function update() {
    if(gameState !== 'PLAYING') return; 

    let lvl = levels[currentLevel];

    if (keys["ArrowRight"] || keys["KeyD"] || touchRight) {
        if (player.velX < player.speed) player.velX++;
        player.facing = "right";
    }
    if (keys["ArrowLeft"] || keys["KeyA"] || touchLeft) {
        if (player.velX > -player.speed) player.velX--;
        player.facing = "left";
    }

    if (player.grounded) {
        player.coyoteCounter = 6; 
    } else {
        if (player.coyoteCounter > 0) player.coyoteCounter--;
    }

    if ((keys["Space"] || keys["ArrowUp"] || keys["KeyW"] || touchJumpIntent) && player.coyoteCounter > 0 && !player.jumping) {
        player.jumping = true; 
        player.velY = -11.5; 
        player.grounded = false;
        player.coyoteCounter = 0; 
        touchJumpIntent = false; 
    }

    player.velX *= friction;
    player.velY += gravity;
    player.grounded = false;

    if (currentLevel === 2 && player.x > 600 && !lvl.goal.triggered) {
        lvl.goal.triggered = true; lvl.goal.x = 50; lvl.goal.y = 500;
    }
    if (currentLevel === 3 && player.jumping && lvl.objects[1].state === 'idle') {
        lvl.objects[1].state = 'falling';
    }

    for (let obj of lvl.objects) {
        if (obj.type === "fake_hole" && player.x > obj.triggerX && obj.active) {
            obj.active = false; 
            let rescue = lvl.objects.find(o => o.type === "rescue_plat");
            if (rescue) rescue.visible = true;
        }
        if (obj.type === "rescue_plat" && obj.visible) {
            obj.y += obj.speed * obj.dir;
            if (obj.y > obj.maxY || obj.y < obj.minY) obj.dir *= -1;
        }
        if (obj.type === "falling_wall") {
            if (obj.state === "idle" && player.x > obj.triggerX) obj.state = "falling";
            if (obj.state === "falling") {
                obj.y += obj.speed;
                if (obj.y >= 110) { obj.y = 110; obj.state = "waiting"; obj.timer = 0; }
            }
            if (obj.state === "waiting") { obj.timer++; if (obj.timer > 30) obj.state = "rising"; }
            if (obj.state === "rising") { obj.y -= 4; if (obj.y <= -250) { obj.y = -250; obj.state = "done"; } }
        }
        if (obj.type === "moving_plat") {
            obj.x += obj.speed * obj.dir;
            if (obj.x > obj.maxX || obj.x < obj.minX) obj.dir *= -1;
        }
        if (obj.type === "surprise_spike" && lvl.goal.triggered) {
            if (player.x < obj.triggerX && obj.y > 450) { obj.active = true; obj.y -= 12; }
        }
        if (obj.type === "ceiling_trap" && obj.state === "falling") {
            obj.y += obj.speed;
            if (obj.y > 350) obj.state = "done";
        }
        if (obj.type === "invisible_bridge" && player.x > obj.triggerX) {
            obj.visible = true;
        }
        if (obj.type === "breaking_ground" && obj.state === "falling") {
            obj.speed += 0.5;
            obj.y += obj.speed;
        }
        if (obj.type === "teleporter" && player.x > obj.x && obj.active) {
            player.x = 50; player.y = 450; player.velX = 0; player.velY = 0;
            obj.color = "rgba(0,0,0,0)"; obj.active = false; 
        }
        if (obj.type === "rocket_plat" && obj.state === "boost") {
            obj.speed -= 0.8;
            obj.y += obj.speed;
        }
        if (obj.type === "final_surprise" && player.x > 680) {
            obj.active = true;
        }

        if (obj.type === "fake_hole" && !obj.active) continue;
        if (obj.type === "rescue_plat" && !obj.visible) continue;
        if (obj.type === "invisible_bridge" && !obj.visible) continue;
        if (obj.type === "final_surprise" && !obj.active) continue;

        if (player.x < obj.x + obj.width && player.x + player.width > obj.x &&
            player.y < obj.y + obj.height && player.y + player.height > obj.y) {
            
            if (obj.type === "trap" || obj.type === "falling_wall" || obj.type === "surprise_spike" || obj.type === "ceiling_trap" || obj.type === "final_surprise") {
                gameState = 'LOSE'; showScreen('loseScreen'); return;
            }
            
            if (obj.type === "breaking_ground" && obj.state === "idle") obj.state = "falling";
            if (obj.type === "rocket_plat" && obj.state === "idle") obj.state = "boost";

            if (player.y + player.height - player.velY <= obj.y + 14) {
                player.grounded = true; player.jumping = false; player.velY = 0; player.y = obj.y - player.height;
                if (obj.type === "moving_plat") player.x += obj.speed * obj.dir;
                if (obj.type === "rescue_plat") player.y += obj.speed * obj.dir;
                if (obj.type === "breaking_ground") player.y += obj.speed;
                if (obj.type === "rocket_plat") player.y += obj.speed;
            } else if (player.y - player.velY >= obj.y + obj.height - 12) {
                player.velY = 0; player.y = obj.y + obj.height;
            }
        }
    }

    player.x += player.velX;
    player.y += player.velY;
    
    if (player.y > canvas.height) { gameState = 'LOSE'; showScreen('loseScreen'); return; }
    if (player.x < 0) player.x = 0;
    if (player.x > canvas.width - player.width) player.x = canvas.width - player.width;

    if (player.x < lvl.goal.x + lvl.goal.width && player.x + player.width > lvl.goal.x &&
        player.y < lvl.goal.y + lvl.goal.height && player.y + player.height > lvl.goal.y) {
        
        if (lvl.goal.isFake) {
            level8Trolled = true; 
            gameState = 'LOSE'; 
            showScreen('loseScreen'); 
            return;
        }

        if (currentLevel + 1 >= unlockedLevels && unlockedLevels < totalLevels) {
            unlockedLevels = currentLevel + 2;
            // Fortschritt in der App sichern
            localStorage.setItem('glitchTripProgressApp', unlockedLevels);
        }

        if (currentLevel < totalLevels - 1) {
            startLevel(currentLevel + 1); 
        } else {
            gameState = 'WIN'; showScreen('winScreen');
        }
        return;
    }

    draw();
    animationFrameId = requestAnimationFrame(update);
}

function draw() {
    let lvl = levels[currentLevel];
    ctx.fillStyle = lvl.bgColor;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "rgba(255, 255, 255, 0.7)";
    clouds.forEach(c => {
        c.x -= 0.5; if(c.x < -100) c.x = 900;
        ctx.beginPath(); ctx.arc(c.x, c.y, c.size, 0, Math.PI*2);
        ctx.arc(c.x+c.size*0.6, c.y-c.size*0.2, c.size*0.8, 0, Math.PI*2); ctx.fill();
    });

    for (let obj of lvl.objects) {
        if (obj.type === "fake_hole" && !obj.active) continue;
        if (obj.type === "rescue_plat" && !obj.visible) continue;
        if (obj.type === "invisible_bridge" && !obj.visible) continue;
        if (obj.type === "final_surprise" && !obj.active) continue;

        ctx.fillStyle = obj.color;
        ctx.fillRect(obj.x, obj.y, obj.width, obj.height);
        if (obj.x === 0 && obj.y === 560) { ctx.fillStyle = "#1e824c"; ctx.fillRect(0, 560, 800, 6); }
    }

    ctx.fillStyle = "#f1c40f";
    ctx.fillRect(lvl.goal.x, lvl.goal.y, lvl.goal.width, lvl.goal.height);

    ctx.fillStyle = "#3498db";
    ctx.fillRect(player.x, player.y, player.width, player.height);
    ctx.fillStyle = "white";
    let eyeX = player.facing === "right" ? player.x + 20 : player.x + 4;
    ctx.fillRect(eyeX, player.y + 10, 6, 6);
}

// SERVICE WORKER FÜR ECHTE WEB-APP GENERIERUNG
if ('serviceWorker' in navigator) {
    const swCode = `self.addEventListener('fetch', function(e) {});`;
    const blob = new Blob([swCode], {type: 'application/javascript'});
    const swUrl = URL.createObjectURL(blob);
    navigator.serviceWorker.register(swUrl).catch(err => console.log(err));
}

initLevels();
showScreen('homeMenu');
update();
</script>
</body>
</html>
