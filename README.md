<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Platform Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: lightblue;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }

        canvas {
            display: block;
            background: skyblue;
            border: 2px solid black;
        }

        #controls {
            position: absolute;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        .button {
            width: 60px;
            height: 60px;
            background: lightgray;
            border: 2px solid black;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            font-weight: bold;
            cursor: pointer;
            user-select: none;
        }

        #restart {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 18px;
            background-color: lightgreen;
            border: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="300" height="450"></canvas>
    <div id="controls">
        <div class="button" id="left">←</div>
        <div class="button" id="jump">↑</div>
        <div class="button" id="right">→</div>
    </div>
    <button id="restart" style="display: none;">Restart</button>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const restartButton = document.getElementById('restart');

        // Level 1 Platform Setup
        const level1Platforms = [
            { x: 0, y: 400, width: 300, height: 50, color: 'green' },
            { x: 100, y: 350, width: 100, height: 20, color: 'brown' },
            { x: 300, y: 300, width: 100, height: 20, color: 'blue' },
            { x: 500, y: 250, width: 100, height: 20, color: 'purple' },
            { x: 700, y: 200, width: 100, height: 20, color: 'orange' },
            { x: 900, y: 150, width: 100, height: 20, color: 'gray' },
            { x: 1100, y: 250, width: 100, height: 20, color: 'pink' },
            { x: 1300, y: 300, width: 100, height: 20, color: 'cyan' },
            { x: 1500, y: 350, width: 100, height: 20, color: 'gold' },
            { x: 1700, y: 400, width: 100, height: 20, color: 'lime' }
        ];

        const initialPlayerState = {
            x: 50,
            y: 300,
            radius: 25,
            color: 'orange',
            speed: 5,
            dx: 0,
            dy: 0,
            gravity: 0.5,
            onGround: false
        };

        let cameraX = 0;
        let gameOver = false;
        let player = { ...initialPlayerState };  // Initialize player with the default state
        let platforms = [...level1Platforms]; // Initialize platforms for level 1

        const resetGame = () => {
            // Reset player state to initial values
            player = { ...initialPlayerState };
            gameOver = false;
            restartButton.style.display = 'none'; // Hide restart button
            platforms = [...level1Platforms]; // Reset to level 1 platforms
            gameLoop(); // Restart the game
        };

        const update = () => {
            if (gameOver) return;

            player.dy += player.gravity;
            player.x += player.dx;
            player.y += player.dy;

            player.onGround = false;
            for (const platform of platforms) {
                if (
                    player.x < platform.x + platform.width &&
                    player.x + player.radius * 2 > platform.x &&
                    player.y + player.radius * 2 > platform.y &&
                    player.y + player.radius * 2 <= platform.y + 10
                ) {
                    player.y = platform.y - player.radius * 2;
                    player.dy = 0;
                    player.onGround = true;
                }
            }

            if (player.y > canvas.height) {
                gameOver = true;
                restartButton.style.display = 'block'; // Show the restart button
            }

            player.x = Math.max(0, player.x);
            player.y = Math.min(canvas.height, player.y);

            cameraX = Math.max(0, player.x - canvas.width / 2);
        };

        const draw = () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            ctx.save();
            ctx.translate(-cameraX, 0);

            for (const platform of platforms) {
                ctx.fillStyle = platform.color;
                ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
            }

            ctx.fillStyle = player.color;
            ctx.beginPath();
            ctx.arc(player.x + player.radius, player.y + player.radius, player.radius, 0, Math.PI * 2);
            ctx.fill();

            ctx.strokeStyle = 'black';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(player.x + player.radius, player.y + player.radius, player.radius, 0, Math.PI * 2);
            ctx.stroke();

            ctx.restore();
        };

        const gameLoop = () => {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        };

        const moveLeft = () => player.dx = -player.speed;
        const moveRight = () => player.dx = player.speed;
        const jump = () => {
            if (player.onGround) {
                player.dy = -10;
            }
        };
        const stop = () => player.dx = 0;

        document.getElementById('left').addEventListener('touchstart', moveLeft);
        document.getElementById('left').addEventListener('touchend', stop);

        document.getElementById('right').addEventListener('touchstart', moveRight);
        document.getElementById('right').addEventListener('touchend', stop);

        document.getElementById('jump').addEventListener('touchstart', jump);

        // Restart the game
        restartButton.addEventListener('click', resetGame);

        gameLoop();
    </script>
</body>
</html>
