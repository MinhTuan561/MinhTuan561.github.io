<!DOCTYPE html>
<html>
<head>
    <title>Chicken Shooter Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f0f0f0;
        }
        canvas {
            border: 1px solid #000;
        }
        #score {
            font-size: 24px;
            font-weight: bold;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>Chicken Shooter Game</h1>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div id="score">Score: 0</div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Tải hình ảnh
        const spaceshipImg = new Image();
        spaceshipImg.src = 'spaceship.png';
        const chickenImg = new Image();
        chickenImg.src = 'chicken.png';

        // Tạo lớp cho phi thuyền
        class Spaceship {
            constructor() {
                this.x = canvas.width / 2;
                this.y = canvas.height - 50;
                this.width = 50;
                this.height = 50;
                this.speed = 5;
            }
            move(direction) {
                if (direction === 'left' && this.x > 0) {
                    this.x -= this.speed;
                } else if (direction === 'right' && this.x < canvas.width - this.width) {
                    this.x += this.speed;
                }
            }
            shoot() {
                bullets.push(new Bullet(this.x + this.width / 2, this.y));
            }
            draw() {
                ctx.drawImage(spaceshipImg, this.x, this.y, this.width, this.height);
            }
        }

        // Tạo lớp cho đạn
        class Bullet {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.width = 5;
                this.height = 10;
                this.speed = 10;
            }
            move() {
                this.y -= this.speed;
            }
            draw() {
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }
        }

        // Tạo lớp cho gà
        class Chicken {
            constructor() {
                this.x = Math.floor(Math.random() * (canvas.width - 50));
                this.y = -50;
                this.width = 50;
                this.height = 50;
                this.speed = Math.floor(Math.random() * 5) + 1;
            }
            move() {
                this.y += this.speed;
                if (this.y > canvas.height) {
                    this.x = Math.floor(Math.random() * (canvas.width - 50));
                    this.y = -50;
                }
            }
            draw() {
                ctx.drawImage(chickenImg, this.x, this.y, this.width, this.height);
            }
        }

        // Khởi tạo các đối tượng
        const spaceship = new Spaceship();
        const chickens = [];
        const bullets = [];
        let score = 0;

        // Tạo gà
        for (let i = 0; i < 10; i++) {
            chickens.push(new Chicken());
        }

        // Vòng lặp chính của trò chơi
        function gameLoop() {
            // Xóa màn hình
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Cập nhật vị trí các đối tượng
            spaceship.draw();
            chickens.forEach(chicken => {
                chicken.move();
                chicken.draw();
            });
            bullets.forEach((bullet, index) => {
                bullet.move();
                bullet.draw();
                // Kiểm tra va chạm giữa đạn và gà
                chickens.forEach((chicken, chickenIndex) => {
                    if (
                        bullet.x >= chicken.x &&
                        bullet.x <= chicken.x + chicken.width &&
                        bullet.y <= chicken.y + chicken.height &&
                        bullet.y >= chicken.y
                    ) {
                        // Bắn trúng gà
                        chickens.splice(chickenIndex, 1);
                        bullets.splice(index, 1);
                        score++;
                        document.getElementById('score').textContent = `Score: ${score}`;
                        chickens.push(new Chicken());
                    }
                });
                if (bullet.y < 0) {
                    bullets.splice(index, 1);
                }
            });

            // Điều khiển phi thuyền
            document.addEventListener('keydown', event => {
                if (event.key === 'ArrowLeft') {
                    spaceship.move('left');
                } else if (event.key === 'ArrowRight') {
                    spaceship.move('right');
                } else if (event.key === ' ') {
                    spaceship.shoot();
                }
            });

            // Gọi lại hàm gameLoop
            requestAnimationFrame(gameLoop);
        }

        // Bắt đầu trò chơi
        gameLoop();
    </script>
</body>
</html>

