#Bullet-hell-p5js

var gameActive = false; 
let fondo;
let player = {}; // Objeto para el jugador
let bullets = []; // Arreglo para los proyectiles
let parabolaBullets = []; // Arreglo para las balas parabólicas
let points = 0;
let nave, asteroide, asteroide2; // Texturas para jugador, asteroides y proyectiles
let button1, button2, button3, button4;
let bulletInterval;
let bulletSpeed = 1000; // Intervalo inicial en milisegundos para los proyectiles

function preload() {
  nave = loadImage("data/nave.png"); // Imagen para el jugador
  asteroide = loadImage("data/Asteroide.png"); // Imagen para los proyectiles
  asteroide2 = loadImage("data/Asteroide.png"); // Imagen para los proyectiles parabola
  fondo = loadImage("data/fondo.png");
}

function setup() {
  createCanvas(windowWidth, windowHeight); 
  background(220);
  menu();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function menu() {
  clear();
  background(fondo);
  textSize(40); 
  fill(220); 
  textAlign(CENTER);
  text("Estado crítico", width / 2, height / 3); 

  button1 = createButton("Inicio");
  button1.position(width / 2 - 50, height / 2);
  button1.mousePressed(empezarjuego);

  button2 = createButton("Créditos");
  button2.position(width / 2 - 50, height / 2 + 40);
  button2.mousePressed(creditos);
}

function creditos() {
  clear();
  background(fondo);
  textSize(25);
  fill(255);
  textAlign(CENTER);
  text("Creador: Bruno Cairo", width / 2, height / 3);
  text("Diseños: Bruno Cairo", width / 2, height / 3 + 50);
  text("Programador: Bruno Cairo", width / 2, height / 3 + 100);

  button3 = createButton("Volver");
  button3.position(width / 2 - 50, height - 100);
  button3.mousePressed(menu);
}

function empezarjuego() {
  gameActive = true;
  points = 0;
  bullets = [];
  parabolaBullets = [];
  clear();
  button1.remove();
  button2.remove();
  player = { x: width / 2, y: height / 2, size: 50 }; // Tamaño ajustado a la textura
  bulletSpeed = 1000;
  bulletInterval = setInterval(createBullet, bulletSpeed);
}

function draw() {
  if (gameActive) {
    background(fondo);
    updatePlayer();
    showPlayer();

    for (let i = bullets.length - 1; i >= 0; i--) {
      updateBullet(bullets[i]);
      showBullet(bullets[i]);
      if (hits(bullets[i], player)) {
        gameOver();
        break;
      }
    }
    
    for (let i = parabolaBullets.length - 1; i >= 0; i--) {
      updateParabolaBullet(parabolaBullets[i]);
      showParabolaBullet(parabolaBullets[i]);
      if (hits(parabolaBullets[i], player)) {
        gameOver();
        break;
      }
    }

    textSize(32);
    fill(255);
    textAlign(LEFT);
    text("Puntos: " + points, 10, 30);

    ajustarVelocidadProyectiles();
  }
}

function touchMoved() {
  let dx = (mouseX - pmouseX) / 9;
  let dy = (mouseY - pmouseY) / 9;
  movePlayer(dx, dy);
  return false;
}

function createBullet() {
  if (gameActive) {
    bullets.push({ x: random(width), y: 0, size: 30, speed: 5 }); // Tamaño ajustado a la textura
    points++;
  }
}

function ajustarVelocidadProyectiles() {
  if (points % 10 === 0 && bulletSpeed > 200) {
    clearInterval(bulletInterval);
    bulletSpeed -= 100;
    bulletInterval = setInterval(createBullet, bulletSpeed);
  }
}

function gameOver() {
  noLoop();
  gameActive = false;
  clearInterval(bulletInterval);
  textSize(32);
  fill(255, 0, 0);
  textAlign(CENTER);
  text("Game Over", width / 2, height / 2);

  button4 = createButton("Volver al menú");
  button4.position(width / 2 - 50, height / 2 + 40);
  button4.mousePressed(() => {
    button4.remove();
    loop();
    menu();
  });
}

function updatePlayer() {
  player.x = constrain(player.x, 0, width);
  player.y = constrain(player.y, 0, height);
}

function showPlayer() {
  image(nave, player.x - player.size / 2, player.y - player.size / 2, player.size, player.size); // Dibuja la nave del jugador
}

function movePlayer(dx, dy) {
  player.x += dx * 5;
  player.y += dy * 5;
}

function updateBullet(bullet) {
  bullet.y += bullet.speed;
}

function showBullet(bullet) {
  image(asteroide, bullet.x - bullet.size / 2, bullet.y - bullet.size / 2, bullet.size, bullet.size); // Dibuja los proyectiles
}

function hits(bullet, player) {
  let d = dist(bullet.x, bullet.y, player.x, player.y);
  return d < bullet.size / 2 + player.size / 2;
}

function createParabolaBullet() {
  if (gameActive) {
    parabolaBullets.push({ 
      x: player.x, 
      y: player.y, 
      t: 0, // Tiempo inicial
      size: 30 // Tamaño del asteroide
    });
    points++;
  }
}

function updateParabolaBullet(parabolaBullets) {
  bullet.t += 0.1; // Incrementa el tiempo para el movimiento
  bullet.x += 5; // Movimiento horizontal

  // Ecuación de la parábola
  bullet.y = -0.1 * (bullet.t - 50) * (bullet.t - 50) + height - 100; 
}

function showParabolaBullet(parabolaBullets) {
  // Dibuja el asteroide usando la textura
  image(asteroide2, parabolaBullets.x - parabolaBullets.size / 2, parabolaBullets.y - parabolaBullets.size / 2, parabolaBullets.size, parabolaBullets.size); 
}

