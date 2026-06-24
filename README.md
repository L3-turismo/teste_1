# teste_1
teste_1

let gameState = "start";

let plane;

let bags = [];

let score = 0;
let lives = 5;

let planeSpeed = 6;

const targetScore = 15;

const correctTerms = [
  "Patrimônio Cultural",
  "Patrimônio Material",
  "Patrimônio Imaterial",
  "Memória",
  "Identidade",
  "Museus",
  "Centros Históricos",
  "Gastronomia",
  "Festas Populares",
  "Artesanato",
  "Arquitetura",
  "Tradições",
  "Folclore",
  "Educação Patrimonial",
  "Interpretação do Patrimônio"
];

const wrongTerms = [
  "Bitcoin",
  "Futebol",
  "Astronomia",
  "Programação",
  "Química",
  "Robótica",
  "Matemática",
  "Basquete"
];

let clouds = [];
let birds = [];

function setup() {
  createCanvas(windowWidth, windowHeight);

  plane = {
    x: width * 2,
    y: height / 2,
    size: 50
  };

  createClouds();
  createBirds();

  textFont("Arial");
}

function draw() {

  drawSky();

  if (gameState === "start") {
    startScreen();
    return;
  }

  if (gameState === "win") {
    winScreen();
    return;
  }

  if (gameState === "lose") {
    loseScreen();
    return;
  }

  updateClouds();
  updateBirds();

  drawClouds();
  drawBirds();

  drawHUD();

  movePlane();
  drawPlane();

  updateBags();
  drawTouchButtons();
}

function startGame() {
  score = 0;
  lives = 5;
  planeSpeed = 6;

  plane.x = width * 2;
  plane.y = height / 2;

  bags = [];

  for (let i = 0; i < 6; i++) {
    spawnBag();
  }

  gameState = "playing";
}

function spawnBag() {

  let isCorrect = random() < 0.7;

  let term;

  if (isCorrect) {
    term = random(correctTerms);
  } else {
    term = random(wrongTerms);
  }

  bags.push({
    x: random(width + 100, width + 600),
    y: random(100, height - 120),
    speed: random(0.5, 2),
    term: term,
    correct: isCorrect
  });
}

function updateBags() {

  for (let i = bags.length - 1; i >= 0; i--) {

    let bag = bags[i];

    bag.x -= bag.speed;

    let d = dist(plane.x, plane.y, bag.x, bag.y);

    if (d < 50) {

      if (bag.correct) {
        score++;
      } else {
        lives--;

        planeSpeed = max(2, planeSpeed - 0.8);

        plane.y += 30;
      }

      bags.splice(i, 1);
      spawnBag();
      continue;
    }

    if (bag.x < -120) {
      bags.splice(i, 1);
      spawnBag();
    }
  }

  if (score >= targetScore) {
    gameState = "win";
  }

  if (lives <= 0) {
    gameState = "lose";
  }
}

function drawBag(bag) {

  rectMode(CENTER);

  fill(139, 69, 19);
  stroke(90, 40, 10);
  strokeWeight(2);

  rect(bag.x, bag.y, 90, 55, 10);

  noFill();
  rect(bag.x, bag.y - 25, 25, 12, 5);

  noStroke();

  fill(255);
  textAlign(CENTER, CENTER);
  textSize(11);

  text(
    bag.term,
    bag.x,
    bag.y,
    80,
    45
  );
}

function drawPlane() {

  push();

  translate(plane.x, plane.y);

  noStroke();

  fill(220);

  ellipse(0, 0, 90, 35);

  triangle(
    45, 0,
    20, -15,
    20, 15
  );

  fill(180);

  triangle(
    -10, 0,
    -45, -25,
    10, -5
  );

  triangle(
    -10, 0,
    -45, 25,
    10, 5
  );

  fill(150);

  triangle(
    -40, 0,
    -55, -20,
    -30, -5
  );

  fill(100, 180, 255);

  ellipse(10, -5, 20, 15);

  pop();
}

function movePlane() {

  if (keyIsDown(LEFT_ARROW)) {
    plane.x -= planeSpeed;
  }

  if (keyIsDown(RIGHT_ARROW)) {
    plane.x += planeSpeed;
  }

  if (keyIsDown(UP_ARROW)) {
    plane.y -= planeSpeed;
  }

  if (keyIsDown(DOWN_ARROW)) {
    plane.y += planeSpeed;
  }

  plane.x = constrain(
    plane.x,
    40,
    width - 40
  );

  plane.y = constrain(
    plane.y,
    70,
    height - 70
  );
}

function drawHUD() {

  fill(255);

  textAlign(LEFT);

  textSize(24);
  text("Pontos: " + score + "/15", 20, 40);

  textSize(24);
  text("Vidas: " + lives, 20, 75);

  textSize(18);
  text(
    "Capacidade de voo: " +
    nf(planeSpeed, 1, 1),
    20,
    105
  );
}

function drawSky() {

  for (let y = 0; y < height; y++) {

    let inter = map(y, 0, height, 0, 1);

    let c = lerpColor(
      color(90, 170, 255),
      color(210, 235, 255),
      inter
    );

    stroke(c);
    line(0, y, width, y);
  }
}

function createClouds() {

  for (let i = 0; i < 10; i++) {

    clouds.push({
      x: random(width),
      y: random(height * 0.7),
      size: random(50, 120)
    });
  }
}

function updateClouds() {

  for (let c of clouds) {

    c.x -= 0.5;

    if (c.x < -150) {
      c.x = width + 100;
      c.y = random(height * 0.7);
    }
  }
}

function drawClouds() {

  noStroke();

  fill(255, 220);

  for (let c of clouds) {

    ellipse(c.x, c.y, c.size);

    ellipse(
      c.x + 30,
      c.y - 10,
      c.size * 0.8
    );

    ellipse(
      c.x - 30,
      c.y - 5,
      c.size * 0.7
    );
  }
}

function createBirds() {

  for (let i = 0; i < 5; i++) {

    birds.push({
      x: random(width),
      y: random(50, 250)
    });
  }
}

function updateBirds() {

  for (let b of birds) {

    b.x -= 2;

    if (b.x < -40) {
      b.x = width + 40;
      b.y = random(50, 250);
    }
  }
}

function drawBirds() {

  stroke(0);

  strokeWeight(2);

  noFill();

  for (let b of birds) {

    arc(b.x, b.y, 20, 10, PI, TWO_PI);

    arc(
      b.x + 20,
      b.y,
      20,
      10,
      PI,
      TWO_PI
    );
  }
}

function startScreen() {

  fill(255);

  textAlign(CENTER);

  textSize(40);
  text(
    "TURISMO CULTURAL\nNA TROPOSFERA",
    width / 2,
    height / 3
  );

  textSize(22);

  text(
    "Colete as malas com termos corretos.\n" +
    "Evite os termos errados.\n\n" +
    "15 pontos para vencer.\n" +
    "5 erros e o jogo acaba.\n" +
    "Cada erro reduz sua capacidade de voo.\n\n" +
    "Clique para começar.",
    width / 2,
    height / 2
  );
}

function winScreen() {

  fill(255);

  textAlign(CENTER);

  textSize(50);
  text(
    "VOCÊ VENCEU!",
    width / 2,
    height / 2 - 50
  );

  textSize(28);

  text(
    "Pontuação: " + score +
    "\nClique para jogar novamente",
    width / 2,
    height / 2 + 30
  );
}

function loseScreen() {

  fill(255);

  textAlign(CENTER);

  textSize(50);

  text(
    "FIM DE JOGO",
    width / 2,
    height / 2 - 50
  );

  textSize(28);

  text(
    "Você perdeu as 5 chances.\n" +
    "Clique para tentar novamente.",
    width / 2,
    height / 2 + 30
  );
}

function drawTouchButtons() {

  if (!('ontouchstart' in window)) return;

  fill(0, 120);

  circle(90, height - 90, 70);
  circle(180, height - 90, 70);

  circle(width - 180, height - 90, 70);
  circle(width - 90, height - 90, 70);

  fill(255);

  textAlign(CENTER, CENTER);

  textSize(24);

  text("←", 90, height - 90);
  text("→", 180, height - 90);

  text("↑", width - 180, height - 90);
  text("↓", width - 90, height - 90);
}

function mousePressed() {

  if (
    gameState === "start" ||
    gameState === "win" ||
    gameState === "lose"
  ) {
    startGame();
  }
}

function touchStarted() {

  if (
    gameState === "start" ||
    gameState === "win" ||
    gameState === "lose"
  ) {
    startGame();
    return false;
  }

  return false;
}

function touchMoved() {

  if (gameState !== "playing") return false;

  for (let t of touches) {

    if (
      dist(t.x, t.y, 90, height - 90) < 35
    ) {
      plane.x -= planeSpeed;
    }

    if (
      dist(t.x, t.y, 180, height - 90) < 35
    ) {
      plane.x += planeSpeed;
    }

    if (
      dist(t.x, t.y, width - 180, height - 90) < 35
    ) {
      plane.y -= planeSpeed;
    }

    if (
      dist(t.x, t.y, width - 90, height - 90) < 35
    ) {
      plane.y += planeSpeed;
    }
  }

  return false;
}

function draw() {

  drawSky();

  if (gameState === "start") {
    startScreen();
    return;
  }

  if (gameState === "win") {
    winScreen();
    return;
  }

  if (gameState === "lose") {
    loseScreen();
    return;
  }

  updateClouds();
  updateBirds();

  drawClouds();
  drawBirds();

  drawHUD();

  movePlane();
  drawPlane();

  for (let bag of bags) {
    drawBag(bag);
  }

  updateBags();

  drawTouchButtons();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}
