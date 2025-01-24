<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>hehehe</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #000;
            color: white;
            margin: 0;
            padding: 0;
        }
        canvas {
            display: block;
            margin: 20px auto;
            background-color: #fff;
        }
        #gameContainer {
            margin-top: 20px;
        }
        button {
            padding: 10px 20px;
            background-color: #ff6f91;
            border: none;
            color: white;
            font-size: 18px;
            cursor: pointer;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <h1>Đi Tìm Tình Yêu</h1>
    <p>Điều khiển trái tim của bạn để vượt qua mê cung và tìm thấy tình yêu đích thực!</p>
    <div id="gameContainer">
        <canvas id="mazeCanvas" width="800" height="600"></canvas>
        <button id="startGame">Start Game</button>
    </div>

    <script>
        const canvas = document.getElementById('mazeCanvas');
        const ctx = canvas.getContext('2d');
        const startButton = document.getElementById('startGame');
        const rows = 30; // Số hàng
        const cols = 40; // Số cột
        const cellSize = canvas.width / cols;
        let player = { x: 1, y: 1 };
        let endPoint = { x: cols - 2, y: rows - 2 }; // Điểm kết thúc

        function generateMaze(rows, cols) {
            const maze = Array.from({ length: rows }, () => Array(cols).fill(1));
            const stack = [];
            const directions = [
                [0, -1],
                [0, 1],
                [-1, 0],
                [1, 0],
            ];

            function shuffle(array) {
                for (let i = array.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [array[i], array[j]] = [array[j], array[i]];
                }
                return array;
            }

            function carvePath(x, y) {
                maze[y][x] = 0;
                stack.push([x, y]);

                while (stack.length > 0) {
                    const [cx, cy] = stack[stack.length - 1];
                    const shuffledDirs = shuffle([...directions]);

                    let carved = false;
                    for (const [dx, dy] of shuffledDirs) {
                        const nx = cx + dx * 2;
                        const ny = cy + dy * 2;

                        if (nx > 0 && ny > 0 && nx < cols - 1 && ny < rows - 1 && maze[ny][nx] === 1) {
                            maze[cy + dy][cx + dx] = 0;
                            maze[ny][nx] = 0;
                            stack.push([nx, ny]);
                            carved = true;
                            break;
                        }
                    }

                    if (!carved) stack.pop();
                }
            }

            carvePath(1, 1);
            maze[endPoint.y][endPoint.x] = 0;

            if (maze[rows - 3][cols - 2] === 1) {
                maze[rows - 3][cols - 2] = 0;
            }

            return maze;
        }

        let maze = generateMaze(rows, cols);

        function drawMaze() {
            for (let row = 0; row < maze.length; row++) {
                for (let col = 0; col < maze[row].length; col++) {
                    ctx.fillStyle = maze[row][col] === 1 ? '#000' : '#fff';
                    ctx.fillRect(col * cellSize, row * cellSize, cellSize, cellSize);
                }
            }

            ctx.fillStyle = 'green';
            ctx.fillRect(1 * cellSize, 1 * cellSize, cellSize, cellSize);

            ctx.fillStyle = 'red';
            ctx.fillRect(endPoint.x * cellSize, endPoint.y * cellSize, cellSize, cellSize);
        }

        function drawPlayer() {
        ctx.fillStyle = '#800080'; // Màu tím của trái tim
        const x = player.x * cellSize + cellSize / 2; // Tâm hình trái tim
        const y = player.y * cellSize + cellSize / 2;
        const size = cellSize / 2.5; // Kích thước trái tim phù hợp với ô

        ctx.beginPath();
        // Vẽ cung tròn trái
        ctx.arc(x - size / 2, y - size / 4, size / 2, Math.PI, 2 * Math.PI);
        // Vẽ cung tròn phải
        ctx.arc(x + size / 2, y - size / 4, size / 2, Math.PI, 2 * Math.PI);
        // Vẽ phần đáy nhọn
        ctx.lineTo(x, y + size);
        ctx.closePath();
        ctx.fill();
        }


        function checkWin() {
            if (player.x === endPoint.x && player.y === endPoint.y) {
                alert('muốn kiếm ngiu thì đi kiếm đi chơi cc :>');
                startGame();
            }
        }

        function movePlayer(dx, dy) {
            const newX = player.x + dx;
            const newY = player.y + dy;

            if (maze[newY][newX] === 0) {
                player.x = newX;
                player.y = newY;
                drawMaze();
                drawPlayer();
                checkWin();
            }
        }

        function startGame() {
            maze = generateMaze(rows, cols);
            player = { x: 1, y: 1 };
            drawMaze();
            drawPlayer();
        }

        document.addEventListener('keydown', (e) => {
            switch (e.key) {
                case 'ArrowUp':
                    movePlayer(0, -1);
                    break;
                case 'ArrowDown':
                    movePlayer(0, 1);
                    break;
                case 'ArrowLeft':
                    movePlayer(-1, 0);
                    break;
                case 'ArrowRight':
                    movePlayer(1, 0);
                    break;
            }
        });

        startButton.addEventListener('click', startGame);
    </script>
</body>
</html>
