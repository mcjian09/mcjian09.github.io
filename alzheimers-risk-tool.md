---
layout: default
title: Alzheimer Risk App
permalink: /alzheimers-risk-tool/
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

  h1, h2, h3 {
    color: #111;
  }

  .back-link {
    display: inline-block;
    margin-bottom: 20px;
    color: #0366d6;
    text-decoration: none;
    font-weight: 600;
  }

  .back-link:hover {
    text-decoration: underline;
  }

  .card {
    border: 1px solid #ddd;
    border-radius: 10px;
    padding: 20px;
    margin: 20px 0;
    background: #fafafa;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
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
    box-sizing: border-box;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 8px;
    font-size: 1rem;
  }

  button {
    margin-top: 18px;
    padding: 10px 16px;
    font-size: 1rem;
    font-weight: 600;
    color: #fff;
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
    border: 1px solid #cfe4ff;
  }

  .note {
    color: #555;
    font-size: 0.95rem;
  }

  .chart-wrap {
    margin-top: 20px;
  }
</style>

<a class="back-link" href="{{ '/alzheimers/' | relative_url }}">&larr; Back to Alzheimer's Research</a>

<h1>Alzheimer's Risk Projection Tool</h1>

<div class="card">
  <p>
    Enter measured values if available. If HIPPOVOL, LATVENT, s.HIPPOVOL, or s.LATVENT
    are left blank, the model uses the built-in trajectory functions.
  </p>

  <div class="grid">
    <div class="field">
      <label for="age">Age</label>
      <input id="age" type="number" step="any" value="65">
    </div>

    <div class="field">
      <label for="H">HIPPOVOL</label>
      <input id="H" type="number" step="any">
    </div>

    <div class="field">
      <label for="L">LATVENT</label>
      <input id="L" type="number" step="any">
    </div>

    <div class="field">
      <label for="sH">s.HIPPOVOL</label>
      <input id="sH" type="number" step="any">
    </div>

    <div class="field">
      <label for="sL">s.LATVENT</label>
      <input id="sL" type="number" step="any">
    </div>

    <div class="field">
      <label for="k">Years Ahead (k)</label>
      <input id="k" type="number" step="1" value="3">
    </div>
  </div>

  <button onclick="compute()">Compute</button>



<div class="card chart-wrap">
  <canvas id="chart" width="600" height="400"></canvas>
</div>

<script>
// ===== MODEL COEFFICIENTS =====

// replace these with YOUR values
const beta = {
  H: -1800,
  sH: -7400,
  L: -6,
  sL: 735,
  age: -0.002
};

// trajectory models (example — replace with your fitted ones)
function H_mean(age) { return 0.005 - 0.00002 * age + 0.0000002 * age * age; }
function L_mean(age) { return 0.02 + 0.0001 * age; }
function sH_mean(age) { return -0.00002 + (-0.0000005) * age; }
function sL_mean(age) { return 0.0005 + 0.00001 * age; }

// ===== CORE FUNCTION =====
function predictOR(t, k, H, L, sH, sL) {
  // fill missing
  if (Number.isNaN(H))  H  = H_mean(t);
  if (Number.isNaN(L))  L  = L_mean(t);
  if (Number.isNaN(sH)) sH = sH_mean(t);
  if (Number.isNaN(sL)) sL = sL_mean(t);

  // evolve (linear version)
  let Hf = H + k * sH;
  let Lf = L + k * sL;

  let sHf = sH;
  let sLf = sL;

  // delta
  let dH = Hf - H;
  let dsH = sHf - sH;
  let dL = Lf - L;
  let dsL = sLf - sL;
  let dage = k;

  let m = beta.H * dH + beta.sH * dsH +
          beta.L * dL + beta.sL * dsL +
          beta.age * dage;

  return Math.exp(m);
}

// ===== UI FUNCTION =====
let chart;

function compute() {
  let t = parseFloat(document.getElementById("age").value);
  let k = parseInt(document.getElementById("k").value, 10);

  let H  = parseFloat(document.getElementById("H").value);
  let L  = parseFloat(document.getElementById("L").value);
  let sH = parseFloat(document.getElementById("sH").value);
  let sL = parseFloat(document.getElementById("sL").value);

  if (Number.isNaN(t)) {
    document.getElementById("output").innerHTML = "Please enter a valid age.";
    return;
  }

  if (Number.isNaN(k) || k < 1) {
    document.getElementById("output").innerHTML = "Please enter a valid whole number for Years Ahead (k).";
    return;
  }

  let OR = predictOR(t, k, H, L, sH, sL);

  document.getElementById("output").innerHTML =
    "OR = " + OR.toFixed(3) +
    " | Increase = " + ((OR - 1) * 100).toFixed(1) + "%";

  // ===== plot baseline curves =====
  let ages = [55, 60, 65, 70, 75];
  let ks = [1, 2, 3, 4, 5];

  let datasets = ages.map(a => {
    return {
      label: "Age " + a,
      data: ks.map(kk => (predictOR(a, kk) - 1) * 100),
      borderWidth: 2,
      fill: false
    };
  });

  // personalized point
  let userData = ks.map(() => null);
  if (k >= 1 && k <= ks.length) {
    userData[k - 1] = (OR - 1) * 100;
  }

  datasets.push({
    label: "You",
    data: userData,
    pointRadius: 6,
    borderWidth: 0
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
      maintainAspectRatio: true,
      scales: {
        x: {
          title: {
            display: true,
            text: "Years Ahead"
          }
        },
        y: {
          title: {
            display: true,
            text: "Percent Increase in OR"
          }
        }
      }
    }
  });
}
</script>
