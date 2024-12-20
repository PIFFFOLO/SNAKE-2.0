const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Impostazioni del gioco
const gridSize = 20;
const canvasSize = 400;
const initialSnakeLength = 3;
const initialSpeed = 100; // in millisecondi
let snake = [];
let snakeDirection = { x: gridSize, y: 0 }; // Direzione iniziale (destra)
let food = {};
let score = 0;
let level = 1;
let gameSpeed = initialSpeed;
let obstacles = [];
let gameInterval;
let isGameOver = false;

// Funzione per inizializzare il gioco
function startGame() {
  snake = [];
  snakeDirection = { x: gridSize, y: 0 };
  score = 0;
  level = 1;
  gameSpeed = initialSpeed;
  obstacles = [];
  isGameOver = false;
  document.getElementById("gameOver").classList.add("hidden");
  document.getElementById("score").innerText = `Punteggio: ${score}`;
  document.getElementById("level").innerText = `Livello: ${level}`;
  createSnake();
  spawnFood();
  spawnObstacles();
  clearInterval(gameInterval);
  gameInterval = setInterval(gameLoop, gameSpeed);
}

// Funzione per disegnare il canvas
function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height); // Pulisce il canvas
  drawSnake();
  drawFood();
  drawObstacles();
}

// Funzione per disegnare il serpente
function drawSnake() {
  ctx.fillStyle = "lime";
  snake.forEach((segment) => {
    ctx.fillRect(segment.x, segment.y, gridSize, gridSize);
  });
}

// Funzione per disegnare il cibo
function drawFood() {
  ctx.fillStyle = "red";
  ctx.fillRect(food.x, food.y, gridSize, gridSize);
}

// Funzione per disegnare gli ostacoli
function drawObstacles() {
  ctx.fillStyle = "gray";
  obstacles.forEach((obstacle) => {
    ctx.fillRect(obstacle.x, obstacle.y, gridSize, gridSize);
  });
}

// Funzione per gestire la logica del gioco
function gameLoop() {
  if (isGameOver) return;

  moveSnake();
  checkCollisions();
  draw();
  updateScore();
}

// Funzione per muovere il serpente
function moveSnake() {
  const newHead = {
    x: snake[0].x + snakeDirection.x,
    y: snake[0].y + snakeDirection.y,
  };
  snake.unshift(newHead);
  if (newHead.x === food.x && newHead.y === food.y) {
    score++;
    if (score % 5 === 0) {
      levelUp();
    }
    spawnFood();
  } else {
    snake.pop();
  }
}

// Funzione per controllare le collisioni
function checkCollisions() {
  const head = snake[0];

  // Collisione con il muro
  if (head.x < 0 || head.x >= canvasSize || head.y < 0 || head.y >= canvasSize) {
    gameOver();
  }

  // Collisione con il corpo del serpente
  for (let i = 1; i < snake.length; i++) {
    if (head.x === snake[i].x && head.y === snake[i].y) {
      gameOver();
    }
  }

  // Collisione con gli ostacoli
  for (let i = 0; i < obstacles.length; i++) {
    if (head.x === obstacles[i].x && head.y === obstacles[i].y) {
      gameOver();
    }
  }
}

// Funzione per aumentare il livello
function levelUp() {
  level++;
  gameSpeed = Math.max(50, gameSpeed - 10); // Aumenta la velocità
  document.getElementById("level").innerText = `Livello: ${level}`;
  clearInterval(gameInterval);
  gameInterval = setInterval(gameLoop, gameSpeed);
}

// Funzione per generare il cibo
function spawnFood() {
  food = {
    x: Math.floor(Math.random() * (canvasSize / gridSize)) * gridSize,
    y: Math.floor(Math.random() * (canvasSize / gridSize)) * gridSize,
  };
}

// Funzione per generare gli ostacoli
function spawnObstacles() {
  obstacles = [];
  for (let i = 0; i < level; i++) {
    obstacles.push({
      x: Math.floor(Math.random() * (canvasSize / gridSize)) * gridSize,
      y: Math.floor(Math.random() * (canvasSize / gridSize)) * gridSize,
    });
  }
}

// Funzione per disegnare il punteggio
function updateScore() {
  document.getElementById("score").innerText = `Punteggio: ${score}`;
}

// Funzione per finire il gioco
function gameOver() {
  isGameOver = true;
  clearInterval(gameInterval);
  document.getElementById("gameOver").classList.remove("hidden");
}

// Funzione per gestire i controlli della tastiera
document.addEventListener("keydown", (e) => {
  if (e.key === "ArrowUp" && snakeDirection.y === 0) {
    snakeDirection = { x: 0, y: -gridSize };
  } else if (e.key === "ArrowDown" && snakeDirection.y === 0) {
    snakeDirection = { x: 0, y: gridSize };
  } else if (e.key === "ArrowLeft" && snakeDirection.x === 0) {
    snakeDirection = { x: -gridSize, y: 0 };
  } else if (e.key === "ArrowRight" && snakeDirection.x === 0) {
    snakeDirection = { x: gridSize, y: 0 };
  }
});

// Funzione per creare il serpente iniziale
function createSnake() {
  snake = [];
  for (
