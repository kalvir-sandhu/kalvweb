---
layout: layouts/home.njk
---

<div id="turtle">

<canvas id="tronCanvas"></canvas>

<script>
    const canvas = document.getElementById('tronCanvas');
    const ctx = canvas.getContext('2d');

    canvas.width = 680;
    canvas.height = 600;

    const beetle = {
        x: 400,
        y: 300,
        size: 10,
        dx: 2, // Current horizontal velocity
        dy: 0, // Current vertical velocity
        color: '#00fbff',
        lastTurn: 0
    };

    function drawBeetle() {
        // Draw the beetle head/body at current position
        ctx.fillStyle = beetle.color;
        ctx.shadowBlur = 15;
        ctx.shadowColor = beetle.color;

        ctx.beginPath();
        ctx.arc(beetle.x, beetle.y, 6, 0, Math.PI * 2);
        ctx.fill();
        ctx.shadowBlur = 0;
    }

    function changeDirection() {
        const directions = [
            { dx: 2, dy: 0 },  // Right
            { dx: -2, dy: 0 }, // Left
            { dx: 0, dy: 2 },  // Down
            { dx: 0, dy: -2 }  // Up
        ];

        // Filter out the direct opposite direction so it doesn't 180-turn into its own tail
        const validDirections = directions.filter(dir =>
            !(dir.dx === -beetle.dx && dir.dy === -beetle.dy)
        );

        const choice = validDirections[Math.floor(Math.random() * validDirections.length)];
        beetle.dx = choice.dx;
        beetle.dy = choice.dy;
    }

    function update() {
        // Move the beetle
        const oldX = beetle.x;
        const oldY = beetle.y;

        beetle.x += beetle.dx;
        beetle.y += beetle.dy;

        // Draw the trail line
        ctx.strokeStyle = beetle.color;
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(oldX, oldY);
        ctx.lineTo(beetle.x, beetle.y);
        ctx.stroke();

        // Screen Wrap logic
        if (beetle.x > canvas.width) beetle.x = 0;
        if (beetle.x < 0) beetle.x = canvas.width;
        if (beetle.y > canvas.height) beetle.y = 0;
        if (beetle.y < 0) beetle.y = canvas.height;

        // Randomly decide to turn based on a timer or "chance"
        // Here, it tries to turn every 500ms to 1s
        const now = Date.now();
        if (now - beetle.lastTurn > Math.random() * 1000 + 500) {
            changeDirection();
            beetle.lastTurn = now;
        }
    }

    function animate() {
        // We don't clear the canvas so the trail stays
        update();
        drawBeetle();
        requestAnimationFrame(animate);
    }

    // Initialize black background
    ctx.fillStyle = '#000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    animate();
</script>



</div>
