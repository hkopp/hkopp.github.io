---
layout: post
category: Recreational Math
tags : [math, art, programming]
---


As you may know from my [last post]({% post_url 2021-05-29-hopalongs %})
my programming journey started with special fractals called hopalongs.
I thought it would nice to revisit this once again using AI.
Since around spring 2026 I stopped writing code by hand and mostly used AI agents for that task.
For this post, I implemented hopalongs in Javascript, so I can embed them on this website.

Drag the sliders and enjoy.

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      background: #0e0e0e;
      color: #ccc;
      font-family: monospace;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      padding: 24px 16px;
      gap: 20px;
    }

    h1 {
      font-size: 1.2rem;
      letter-spacing: 0.15em;
      color: #eee;
      font-weight: normal;
    }

    canvas {
      display: block;
      background: #000;
      border: 1px solid #333;
      width: min(700px, 100vw - 32px);
      height: min(700px, 100vw - 32px);
      image-rendering: pixelated;
    }

    .controls {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 14px 32px;
      width: min(700px, 100vw - 32px);
    }

    .slider-row {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    .slider-label {
      display: flex;
      justify-content: space-between;
      font-size: 0.85rem;
    }

    .slider-label span:first-child {
      color: #aaa;
    }

    .slider-label .val {
      color: #7df;
      min-width: 5ch;
      text-align: right;
    }

    input[type=range] {
      -webkit-appearance: none;
      width: 100%;
      height: 4px;
      background: #333;
      border-radius: 2px;
      outline: none;
      cursor: pointer;
    }

    input[type=range]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 14px;
      height: 14px;
      border-radius: 50%;
      background: #7df;
      cursor: pointer;
    }

    input[type=range]::-moz-range-thumb {
      width: 14px;
      height: 14px;
      border-radius: 50%;
      background: #7df;
      border: none;
      cursor: pointer;
    }

    .palette-row {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      width: min(700px, 100vw - 32px);
    }

    .swatch {
      flex: 1 1 90px;
      height: 28px;
      border-radius: 4px;
      cursor: pointer;
      border: 2px solid transparent;
      position: relative;
      transition: border-color 0.1s;
    }

    .swatch.active {
      border-color: #fff;
    }

    .swatch span {
      position: absolute;
      bottom: 2px;
      left: 6px;
      font-size: 0.7rem;
      color: rgba(255,255,255,0.85);
      text-shadow: 0 1px 2px rgba(0,0,0,0.8);
      pointer-events: none;
      letter-spacing: 0.05em;
    }

    #status {
      font-size: 0.75rem;
      color: #555;
      height: 1em;
    }
  </style>
</head>
<body>
  <canvas id="canvas"></canvas>

  <div class="controls">
    <div class="slider-row">
      <div class="slider-label"><span>a</span><span class="val" id="val-a"></span></div>
      <input type="range" id="sl-a" min="-3" max="3" step="0.01" value="0.4">
    </div>
    <div class="slider-row">
      <div class="slider-label"><span>b</span><span class="val" id="val-b"></span></div>
      <input type="range" id="sl-b" min="-3" max="3" step="0.01" value="1.0">
    </div>
    <div class="slider-row">
      <div class="slider-label"><span>c</span><span class="val" id="val-c"></span></div>
      <input type="range" id="sl-c" min="-3" max="3" step="0.01" value="0.0">
    </div>
    <div class="slider-row">
      <div class="slider-label"><span>n (points)</span><span class="val" id="val-n"></span></div>
      <input type="range" id="sl-n" min="1000" max="500000" step="1000" value="100000">
    </div>
  </div>
  <div></div>
  <br>

  <div class="palette-row" id="palette-row"></div>

  <div id="status"></div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');

    // Set canvas resolution to match display size
    const SIZE = 700;
    canvas.width = SIZE;
    canvas.height = SIZE;

    const sliders = {
      a: document.getElementById('sl-a'),
      b: document.getElementById('sl-b'),
      c: document.getElementById('sl-c'),
      n: document.getElementById('sl-n'),
    };

    const vals = {
      a: document.getElementById('val-a'),
      b: document.getElementById('val-b'),
      c: document.getElementById('val-c'),
      n: document.getElementById('val-n'),
    };

    // --- Palette system ---
    // Each palette is a list of [r,g,b] stops evenly spaced over t ∈ [0,1].
    function lerp3(stops, t) {
      const n = stops.length - 1;
      const scaled = t * n;
      const i = Math.min(Math.floor(scaled), n - 1);
      const f = scaled - i;
      const a = stops[i], b = stops[i + 1];
      return [
        Math.round(a[0] + (b[0] - a[0]) * f),
        Math.round(a[1] + (b[1] - a[1]) * f),
        Math.round(a[2] + (b[2] - a[2]) * f),
      ];
    }

    const PALETTES = [
      {
        name: 'cyan',
        stops: [[0,0,0],[0,30,180],[0,200,255],[255,255,255]],
        css: 'linear-gradient(to right, #000, #001eb4, #00c8ff, #fff)',
      },
      {
        name: 'fire',
        stops: [[0,0,0],[180,0,0],[255,120,0],[255,220,0],[255,255,255]],
        css: 'linear-gradient(to right, #000, #b40000, #ff7800, #ffdc00, #fff)',
      },
      {
        name: 'plasma',
        stops: [[0,0,30],[80,0,160],[200,0,130],[255,180,0]],
        css: 'linear-gradient(to right, #00001e, #5000a0, #c80082, #ffb400)',
      },
      {
        name: 'viridis',
        stops: [[10,0,50],[30,50,160],[0,140,130],[80,200,60],[250,230,0]],
        css: 'linear-gradient(to right, #0a0032, #1e32a0, #008c82, #50c83c, #fae600)',
      },
      {
        name: 'gold',
        stops: [[0,0,0],[100,10,0],[200,130,0],[255,220,100],[255,255,255]],
        css: 'linear-gradient(to right, #000, #640a00, #c88200, #ffdc64, #fff)',
      },
      {
        name: 'mono',
        stops: [[0,0,0],[255,255,255]],
        css: 'linear-gradient(to right, #000, #fff)',
      },
    ];

    let activePalette = 0;

    // Build swatch elements
    const paletteRow = document.getElementById('palette-row');
    PALETTES.forEach((pal, idx) => {
      const sw = document.createElement('div');
      sw.className = 'swatch' + (idx === 0 ? ' active' : '');
      sw.style.background = pal.css;
      sw.innerHTML = `<span>${pal.name}</span>`;
      sw.addEventListener('click', () => {
        paletteRow.querySelectorAll('.swatch').forEach(s => s.classList.remove('active'));
        sw.classList.add('active');
        activePalette = idx;
        scheduleDraw();
      });
      paletteRow.appendChild(sw);
    });
    // --- End palette system ---

    let renderTimer = null;

    function getParams() {
      return {
        a: parseFloat(sliders.a.value),
        b: parseFloat(sliders.b.value),
        c: parseFloat(sliders.c.value),
        n: parseInt(sliders.n.value),
      };
    }

    function updateLabels(p) {
      vals.a.textContent = p.a.toFixed(2);
      vals.b.textContent = p.b.toFixed(2);
      vals.c.textContent = p.c.toFixed(2);
      vals.n.textContent = p.n.toLocaleString();
    }

    function draw() {
      const p = getParams();
      updateLabels(p);

      const { a, b, c, n } = p;

      // Generate points
      let x = 0, y = 0;
      const xs = new Float64Array(n);
      const ys = new Float64Array(n);

      let minX = Infinity, maxX = -Infinity;
      let minY = Infinity, maxY = -Infinity;

      for (let i = 0; i < n; i++) {
        const nx = y - Math.sign(x) * Math.sqrt(Math.abs(b * x - c));
        const ny = a - x;
        x = nx;
        y = ny;
        xs[i] = x;
        ys[i] = y;
        if (x < minX) minX = x;
        if (x > maxX) maxX = x;
        if (y < minY) minY = y;
        if (y > maxY) maxY = y;
      }

      // Render to canvas
      const PAD = 24;
      const plotW = SIZE - 2 * PAD;
      const plotH = SIZE - 2 * PAD;

      const rangeX = maxX - minX || 1;
      const rangeY = maxY - minY || 1;

      // Keep aspect ratio by using the same scale for both axes
      const scale = Math.min(plotW / rangeX, plotH / rangeY);
      const offsetX = PAD + (plotW - rangeX * scale) / 2;
      const offsetY = PAD + (plotH - rangeY * scale) / 2;

      const imageData = ctx.createImageData(SIZE, SIZE);
      const data = imageData.data;

      // Fill background black (already 0, just set alpha)
      for (let i = 3; i < data.length; i += 4) data[i] = 255;

      // Accumulate hit counts for brightness scaling
      const hits = new Uint32Array(SIZE * SIZE);
      let maxHits = 0;

      for (let i = 0; i < n; i++) {
        const px = Math.round(offsetX + (xs[i] - minX) * scale);
        const py = Math.round(offsetY + (maxY - ys[i]) * scale); // flip Y
        if (px >= 0 && px < SIZE && py >= 0 && py < SIZE) {
          const idx = py * SIZE + px;
          hits[idx]++;
          if (hits[idx] > maxHits) maxHits = hits[idx];
        }
      }

      // Color: map log-brightness through the selected palette
      const logMax = Math.log1p(maxHits);
      const pal = PALETTES[activePalette];
      for (let i = 0; i < SIZE * SIZE; i++) {
        if (hits[i] === 0) continue;
        const t = Math.log1p(hits[i]) / logMax; // 0..1
        const base = i * 4;
        const [r, g, b] = lerp3(pal.stops, t);
        data[base]     = r;
        data[base + 1] = g;
        data[base + 2] = b;
      }

      ctx.putImageData(imageData, 0, 0);
      document.getElementById('status').textContent =
        `rendered ${n.toLocaleString()} points`;
    }

    // Debounce redraws while dragging
    function scheduleDraw() {
      clearTimeout(renderTimer);
      renderTimer = setTimeout(draw, 60);
    }

    Object.values(sliders).forEach(sl => sl.addEventListener('input', scheduleDraw));

    // Initial draw
    draw();
  </script>
</body>
