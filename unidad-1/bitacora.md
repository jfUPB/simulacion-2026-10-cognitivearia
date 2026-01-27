# Unidad 1

## Bitácora de proceso de aprendizaje
#Generative art concepts video: The roots.
"The imagined machine produces the work", it refers o when theres is automization and randomiation in the creation of art. Randomness is a way for the artist to have various possibilities and an array of variation where they can choose from. It has existed for decades, maybe even all the way back to the 50s where people would have art that can be considered generative from a technological perspective abd then even further back when generative art doesnt require coding, but entropy and randonmess. and art made from rthe role of dice for example could be use to interpret it into a physical thing. it has evolved and came up as visual art during the early 20th centurywith conceptual artist like duchamp, who really put the importance of tha artist as secondary, and let room for the randomization or the machine to take an impornat part in the creation process. toilet. Milestones: Hand in hand with the advancemente of technology


## Bitácora de aplicación 
let walkerA; // líder (con Lévy)
let walkerB; // seguidor (sesgado hacia A, sin Lévy)

function setup() {
  createCanvas(640, 240);
  walkerA = new WalkerLevy(width / 2, height / 2);

  // Aparece en un punto diferente (puedes cambiarlo si quieres)
  walkerB = new WalkerBiased(width * 0.2, height * 0.8, walkerA);

  background(35);
}

function draw() {
  walkerA.step();
  walkerA.show();

  walkerB.step();
  walkerB.show();
}

/* ---------- WALKER A: Lévy + colores por dirección ---------- */
class WalkerLevy {
  constructor(x, y) {
    this.x = x;
    this.y = y;

    this.size = 6;

    // Colores por dirección
    this.cRight = color(255, 0, 0);     // derecha
    this.cLeft  = color(0, 255, 0);     // izquierda
    this.cDown  = color(0, 140, 255);   // abajo
    this.cUp    = color(255, 220, 0);   // arriba
    this.col = color(255);

    // Lévy
    this.minStep = 1;
    this.maxStep = 80;
    this.alphaLeft = 2.6;
    this.alphaRight = 0.9;
  }

  show() {
    noStroke();
    fill(this.col);
    ellipse(this.x, this.y, this.size, this.size);
  }

  getAlphaFromMouse() {
    const x = constrain(mouseX, 0, width);
    return map(x, 0, width, this.alphaLeft, this.alphaRight);
  }

  levyStep(alpha) {
    const r = random(1);
    let step = this.minStep * pow(1 - r, -1 / alpha);
    return constrain(step, this.minStep, this.maxStep);
  }

  step() {
    const alpha = this.getAlphaFromMouse();
    const stepSize = this.levyStep(alpha);

    const choice = floor(random(4));

    if (choice === 0) {
      this.x += stepSize; this.col = this.cRight;
    } else if (choice === 1) {
      this.x -= stepSize; this.col = this.cLeft;
    } else if (choice === 2) {
      this.y += stepSize; this.col = this.cDown;
    } else {
      this.y -= stepSize; this.col = this.cUp;
    }

    this.x = constrain(this.x, 0, width - 1);
    this.y = constrain(this.y, 0, height - 1);
  }
}

/* ---------- WALKER B: sesgado hacia Walker A, sin Lévy ---------- */
class WalkerBiased {
  constructor(x, y, targetWalker) {
    this.x = x;
    this.y = y;

    this.size = 6;
    this.target = targetWalker;

    // Paso fijo (SIN Lévy)
    this.stepSize = 1;

    // Intensidad del sesgo hacia el líder
    this.biasStrength = 0.6;

    // Parámetros de la gaussiana para el gris (ajústalos a gusto)
    this.grayMean = 200; // promedio (más alto => más claro)
    this.grayStd = 35;   // desviación (más alto => más variación)
    this.col = color(255); // inicial
  }

  show() {
    noStroke();
    fill(this.col);
    ellipse(this.x, this.y, this.size, this.size);
  }

  step() {
    // 1) Color gris gaussiano (cambia cada paso)
    const g = constrain(randomGaussian(this.grayMean, this.grayStd), 0, 255);
    this.col = color(g);

    // 2) Sesgo hacia el líder
    const dx = this.target.x - this.x;
    const dy = this.target.y - this.y;

    const doBiased = random(1) < this.biasStrength;
    let choice;

    if (doBiased) {
      if (abs(dx) > abs(dy)) {
        choice = (dx > 0) ? 0 : 1; // derecha / izquierda
      } else {
        choice = (dy > 0) ? 2 : 3; // abajo / arriba
      }
    } else {
      choice = floor(random(4));
    }

    // 3) Movimiento (paso fijo)
    if (choice === 0) this.x += this.stepSize;
    else if (choice === 1) this.x -= this.stepSize;
    else if (choice === 2) this.y += this.stepSize;
    else this.y -= this.stepSize;

    this.x = constrain(this.x, 0, width - 1);
    this.y = constrain(this.y, 0, height - 1);
  }
}




## Bitácora de reflexión

Explicación de Levy
import numpy as np
import matplotlib.pyplot as plt

# Parameters
minStep = 1
alpha = 1.5

# Independent variable r (avoid 1 to prevent infinity)
r = np.linspace(0, 0.999, 1000)

# Function
step = minStep * (1 - r) ** (-1 / alpha)

# Plot
plt.figure()
plt.plot(r, step)
plt.xlabel("r")
plt.ylabel("step(r)")
plt.title("Lévy step size: step = minStep * (1 - r)^(-1/alpha)")
plt.show()

<img width="632" height="418" alt="image" src="https://github.com/user-attachments/assets/e66ffb86-e3dd-4403-b33d-f385422959c7" />




