---
layout: default
title: Personalized MRI Scheduling for Early Detection of Brain Atrophy
permalink: /alzheimers-MRI-timing/
---

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
  body {
    font-family: Arial, sans-serif;
    max-width: 960px;
    margin: 40px auto;
    padding: 0 20px;
    line-height: 1.5;
    color: #222;
    background: #fff;
  }

  h1 {
    color: #111;
  }

  .card {
    border: 1px solid #ddd;
    border-radius: 10px;
    padding: 20px;
    margin: 20px 0;
    background: #fafafa;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 14px;
  }

  .field label {
    display: block;
    font-weight: 600;
    margin-bottom: 6px;
  }

  .field input {
    width: 100%;
    padding: 10px;
    border-radius: 8px;
    border: 1px solid #ccc;
  }

  button {
    margin-top: 18px;
    padding: 10px 16px;
    font-weight: 600;
    color: white;
    background: #0366d6;
    border: none;
    border-radius: 8px;
    cursor: pointer;
  }

  button:hover {
    background: #024ea2;
  }

  #output {
    margin-top: 18px;
    padding: 14px;
    border-radius: 8px;
    background: #eef6ff;
  }

  .chart-wrap {
    margin-top: 20px;
  }
</style>

<h1>Alzheimer's Risk Projection Tool</h1>

<div class="card">
  <p>
    Only age is required. 
    HIPPOVOL, LATVENT, and s.HIPPOVOL are volumes that have been standardized by total intracranial volume to adjust for head size. If they are missing, the model will estimate them using population trajectories.
  </p>

  <div class="grid">
    <div class="field">
      <label>Age</label>
      <input id="age" type="number" value="65">
    </div>

    <div class="field">
      <label>HIPPOVOL</label>
      <input id="H" type="number">
    </div>

    <div class="field">
      <label>LATVENT</label>
      <input id="L" type="number">
    </div>

    <div class="field">
      <label>s.HIPPOVOL</label>
      <input id="sH" type="number">
    </div>

    <div class="field">
      <label>Years Ahead (k)</label>
      <input id="k" type="number" value="3">
    </div>
  </div>

  <button onclick="compute()">Compute</button>

  <div id="output"></div>
</div>

<div class="card chart-wrap">
  <canvas id="chart"></canvas>
</div>

<script>
// ===== MODEL COEFFICIENTS =====
const beta = {
  H: -1808.712,
  sH: -7411.386,
  L: -6.031705,
  sL: 734.9819,
  age: -0.002034521
};

// ===== TRAJECTORY MODELS =====

// Hippocampus (quadratic)
function H_mean(age) {
  return 0.00305 + 0.00006103 * age - 0.0000005171 * age * age;
}

// sH linear
function sH_mean(age) {
  return 0.0001864 - 0.000003104 * age;
}

// LATVENT exponential: log(L) = b0 + b1 * age
const b0 = -6.076709;  // replace
const b1 = 0.02953997;            // replace

function L_mean(age) {
  return Math.exp(b0 + b1 * age);
}

// ===== CORE MODEL =====
function predictOR(t, k, H, L, sH) {

  if (H === undefined || Number.isNaN(H)) H = H_mean(t);
  if (L === undefined || Number.isNaN(L)) L = L_mean(t);
  if (sH === undefined || Number.isNaN(sH)) sH = sH_mean(t);

  let sL = b1 * L;

  // evolve H
  let a_H = -0.000003103993; // replace
  let sH_mid = sH + (k/2) * a_H;
  let Hf = H + k * sH_mid;
  let sHf = sH + k * a_H;

  // evolve L
  let Lf = L * Math.exp(b1 * k);
  let sLf = b1 * Lf;

  let dH = Hf - H;
  let dsH = sHf - sH;
  let dL = Lf - L;
  let dsL = sLf - sL;

  let m = beta.H * dH +
          beta.sH * dsH +
          beta.L * dL +
          beta.sL * dsL +
          beta.age * k;

  return Math.exp(m);
}

// ===== UI =====
let chart;

function compute() {

  let t = parseFloat(document.getElementById("age").value);
  let k = parseInt(document.getElementById("k").value);

  let H  = parseFloat(document.getElementById("H").value);
  let L  = parseFloat(document.getElementById("L").value);
  let sH = parseFloat(document.getElementById("sH").value);

  let OR = predictOR(t, k, H, L, sH);

  document.getElementById("output").innerHTML =
    "OR = " + OR.toFixed(3) +
    " | Increase = " + ((OR - 1) * 100).toFixed(1) + "%";

  // ===== Plot =====
  let ages = [50, 60, 70, 80];
  let ks = [1,2,3,4];

  const lineColors = [
    "#e41a1c", // red
    "#ff7f00", // orange
    "#4daf4a", // green
    "#377eb8", // blue
    "#984ea3"  // purple
  ];

  let datasets = ages.map((a, i) => ({
    label: "Age " + a,
    data: ks.map(kk => (predictOR(a, kk) - 1) * 100),
    borderColor: lineColors[i],
    backgroundColor: lineColors[i],
    borderWidth: 2,
    fill: false,
    pointRadius: 3,
    pointHoverRadius: 5
  }));

  // personalized curve
  let userCurve = ks.map(kk => (predictOR(t, kk, H, L, sH) - 1) * 100);

  datasets.push({
    label: "You",
    data: userCurve,
    borderColor: "black",
    backgroundColor: "black",
    borderWidth: 3,
    borderDash: [5,5],
    pointRadius: 4,
    pointHoverRadius: 6,
    fill: false
  });

  if (chart) chart.destroy();

  chart = new Chart(document.getElementById("chart"), {
    type: "line",
    data: {
      labels: ks,
      datasets: datasets
    },
    options: {
      responsive: true,
      scales: {
        x: { title: { display: true, text: "Years Ahead (k)" }},
        y: { title: { display: true, text: "% Increase in Odds" }}
      }
    }
  });
}
</script>
