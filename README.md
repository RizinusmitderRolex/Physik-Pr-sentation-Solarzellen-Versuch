<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<title>Solarzellen Simulation – Detailreich</title>
<style>
    body {
        background: #eef2f3;
        font-family: Arial, sans-serif;
        text-align: center;
        padding: 20px;
    }
    #canvas {
        background: #ffffff;
        border: 2px solid #444;
        margin-top: 20px;
        border-radius: 10px;
    }
    .slider-container {
        width: 400px;
        margin: 0 auto;
    }
</style>
</head>
<body>

<h1>☀️ Interaktive Solarzellen-Simulation (Detailversion)</h1>
<p>Bewege den Regler, um die Lichtstärke zu ändern.</p>

<div class="slider-container">
    <label>Lichtstärke: </label>
    <input type="range" id="lightSlider" min="0" max="100" value="50">
</div>

<canvas id="canvas" width="600" height="350"></canvas>

<p id="values" style="font-size: 18px; font-weight: bold;"></p>

<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
const slider = document.getElementById("lightSlider");
const values = document.getElementById("values");

let electrons = [];

// Elektronen erzeugen
for (let i = 0; i < 50; i++) {
    electrons.push({
        x: Math.random() * 500 + 50,
        y: Math.random() * 200 + 100,
        speed: Math.random() * 1 + 0.5
    });
}

function drawSolarCell(light) {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Lichtstrahlen (mehr Details)
    for (let i = 0; i < light / 4; i++) {
        ctx.strokeStyle = `rgba(255, 230, 100, ${0.15 + light / 400})`;
        ctx.lineWidth = 1;
        ctx.beginPath();
        ctx.moveTo(300 + (Math.random() * 40 - 20), 0);
        ctx.lineTo(Math.random() * 600, 140);
        ctx.stroke();
    }

    // Solarzellenkörper (leicht texturiert)
    const cellX = 80, cellY = 150, cellW = 440, cellH = 130;

    // Grundfläche
    const gradient = ctx.createLinearGradient(0, 150, 0, 280);
    gradient.addColorStop(0, "#2e4f7a");
    gradient.addColorStop(1, "#1d3654");
    ctx.fillStyle = gradient;
    ctx.fillRect(cellX, cellY, cellW, cellH);

    // Feine Textur (dünne diagonale Linien)
    ctx.strokeStyle = "rgba(255,255,255,0.05)";
    for (let i = 0; i < 80; i++) {
        ctx.beginPath();
        ctx.moveTo(cellX + i * 6, cellY);
        ctx.lineTo(cellX + i * 6 - 40, cellY + cellH);
        ctx.stroke();
    }

    // Rahmen
    ctx.strokeStyle = "#102133";
    ctx.lineWidth = 4;
    ctx.strokeRect(cellX, cellY, cellW, cellH);

    // Metallkontakte (Finger)
    ctx.strokeStyle = "#d0d4d7";
    ctx.lineWidth = 3;
    for (let i = 0; i < 8; i++) {
        const fx = cellX + 40 + i * 50;
        ctx.beginPath();
        ctx.moveTo(fx, cellY - 15);
        ctx.lineTo(fx, cellY + cellH + 15);
        ctx.stroke();
    }

    // Hauptkontakt oben
    ctx.lineWidth = 6;
    ctx.beginPath();
    ctx.moveTo(cellX, cellY + 10);
    ctx.lineTo(cellX + cellW, cellY + 10);
    ctx.stroke();

    // Glanzeffekt (bei viel Licht stärker)
    if (light > 20) {
        const shine = ctx.createLinearGradient(cellX, cellY, cellX + cellW, cellY + cellH);
        shine.addColorStop(0, `rgba(255,255,255,${0.02 + light / 500})`);
        shine.addColorStop(1, "rgba(255,255,255,0)");
        ctx.fillStyle = shine;
        ctx.fillRect(cellX, cellY, cellW, cellH);
    }

    // Elektronen bewegen
    electrons.forEach(e => {
        e.x += e.speed * (light / 40);
        if (e.x > cellX + cellW - 20) e.x = cellX + 10;

        ctx.fillStyle = "#00c8ff";
        ctx.beginPath();
        ctx.arc(e.x, e.y, 4, 0, Math.PI * 2);
        ctx.fill();
    });

    // Werte berechnen
    const voltage = (light / 100 * 0.6).toFixed(2);
    const current = (light / 100 * 35).toFixed(1);

    values.innerText = `Spannung: ${voltage} V   |   Strom: ${current} mA`;
}

function animate() {
    const light = parseInt(slider.value, 10);
    drawSolarCell(light);
    requestAnimationFrame(animate);
}

animate();
</script>

</body>
</html>
