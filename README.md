import base64

html_content = """<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Road Rush Game</title>
    <style>
        body { margin: 0; background: #111; color: #fff; text-align: center; font-family: Arial, sans-serif; overflow: hidden; }
        h1 { margin: 10px 0; color: gold; }
        #gameCanvas { background: #333; border: 4px solid gold; box-shadow: 0 0 20px rgba(255, 215, 0, 0.5); display: block; margin: 0 auto; }
        #score { font-size: 20px; margin-top: 10px; }
    </style>
</head>
<body>
    <h1>Road Rush Game</h1>
    <div id="score">Score: 0</div>
    <canvas id="gameCanvas" width="300" height="500"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let carX = 125, carY = 400, carWidth = 40, carHeight = 70, score = 0;
        let obstacleX = Math.floor(Math.random() * 250), obstacleY = -100, obstacleSpeed = 4, isGameOver = false;

        document.addEventListener("keydown", (e) => {
            if (e.key === "ArrowLeft" && carX > 0) carX -= 20;
            else if (e.key === "ArrowRight" && carX < canvas.width - carWidth) carX += 20;
        });

        canvas.addEventListener("click", (e) => {
            let rect = canvas.getBoundingClientRect();
            let clickX = e.clientX - rect.left;
            if (clickX < canvas.width / 2 && carX > 0) carX -= 30;
            else if (clickX >= canvas.width / 2 && carX < canvas.width - carWidth) carX += 30;
        });

        function update() {
            if (isGameOver) return;
            obstacleY += obstacleSpeed;
            if (obstacleY > canvas.height) {
                obstacleY = -100;
                obstacleX = Math.floor(Math.random() * (canvas.width - carWidth));
                score += 10;
                obstacleSpeed += 0.2;
            }
            if (carX < obstacleX + carWidth && carX + carWidth > obstacleX && carY < obstacleY + carHeight && carY + carHeight > obstacleY) {
                isGameOver = true;
            }
        }

        function draw() {
            ctx.fillStyle = "#444";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.strokeStyle = "#fff";
            ctx.setLineDash([20, 20]);
            ctx.beginPath();
            ctx.moveTo(canvas.width / 2, 0);
            ctx.lineTo(canvas.width / 2, canvas.height);
            ctx.stroke();

            ctx.fillStyle = "gold";
            ctx.fillRect(carX, carY, carWidth, carHeight);
            ctx.fillStyle = "red";
            ctx.fillRect(obstacleX, obstacleY, carWidth, carHeight);

            document.getElementById("score").innerText = "Score: " + score;

            if (isGameOver) {
                ctx.fillStyle = "rgba(0, 0, 0, 0.8)";
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = "white";
                ctx.font = "24px Arial";
                ctx.fillText("Game Over!", 85, 230);
                ctx.font = "16px Arial";
                ctx.fillText("Tap to Restart", 100, 270);
            }
        }

        function loop() {
            update();
            draw();
            requestAnimationFrame(loop);
        }

        canvas.addEventListener("click", () => {
            if (isGameOver) {
                isGameOver = false;
                score = 0;
                obstacleY = -100;
                obstacleSpeed = 4;
                carX = 125;
            }
        });

        loop();
    </script>
</body>
</html>"""

with open("index.html", "w", encoding="utf-8") as f:
    f.write(html_content)
