<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>i like donuts</title>
<style>
  body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
    background: #fff;
    gap: 16px;
    font-family: "Comic Sans MS", "Comic Sans", cursive;
  }
  .controls {
    display: flex;
    gap: 24px;
  }
  .control-group {
    display: flex;
    gap: 8px;
    align-items: center;
  }
  button.option-btn {
    padding: 6px 12px;
    border: 2px solid #ddd;
    border-radius: 6px;
    background: #fafafa;
    cursor: pointer;
    font-size: 13px;
    font-family: inherit;
  }
  button.option-btn.active {
    border-color: #333;
    background: #eee;
    font-weight: bold;
  }
  #donut-container {
    position: relative;
    width: 300px;
    height: 300px;
    cursor: pointer;
    /* ts the donut shadow */
    filter: drop-shadow(0 8px 10px rgba(0, 0, 0, 0.18));
  }
  /* the "bread" layer */
  #donut {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background: #fcdbac;
    box-shadow: inset 0 -10px 15px rgba(0,0,0,0.1);
    position: relative;
  }
  #donut::after {
    content: "";
    position: absolute;
    top: 39%;
    left: 39%;
    width: 22%;
    height: 22%;
    background: #fff;
    border-radius: 50%;
    /* existing inset depth + new soft halo around the hole's rim */
    box-shadow: inset 0 5px 10px rgba(0,0,0,0.08), 0 0 10px rgba(0,0,0,0.12);
  }
  #frosting-svg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  /* i think this is the soft shadow on the frosting's hole??? chained with the squiggly wobble */
  #frosting-hole {
    filter: url(#squiggly) drop-shadow(0 0 3px rgba(0, 0, 0, 0.15));
  }
  .sprinkle {
    position: absolute;
    width: 4px;
    height: 17px;
    border-radius: 2px;
    pointer-events: none;
    transform-origin: center;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
  }
  .sprinkle.dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
  }

/* timer */
#time-wasted {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  color: red;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
  font-size: 16px;
  margin: 0;
}
</style>
</head>
<body>
<div id="donut-container">
  <div id="donut"></div>
  <svg id="frosting-svg" viewBox="0 0 300 300">
    <defs>
      <filter id="squiggly" x="-20%" y="-20%" width="140%" height="140%">
        <feTurbulence type="fractalNoise" baseFrequency="0.015" numOctaves="2" seed="7" result="noise" />
        <feDisplacementMap in="SourceGraphic" in2="noise" scale="14" xChannelSelector="R" yChannelSelector="G" />
      </filter>
    </defs>
    <circle id="frosting-main" cx="150" cy="150" r="140" fill="#ff8fc7" filter="url(#squiggly)" />
    <circle id="frosting-hole" cx="150" cy="150" r="33" fill="#fff" />
  </svg>
</div>
<div class="controls">
  <div class="control-group">
    <button class="option-btn active" data-frosting="#ff8fc7">pink</button>
    <button class="option-btn" data-frosting="#6b3f2a">chocolate</button>
    <button class="option-btn" data-frosting="#fbeeda">coom</button>
  </div>
  <div class="control-group">
    <button class="option-btn active" data-shape="long">sprinkles</button>
    <button class="option-btn" data-shape="dot">nonpareils</button>
  </div>
  <div class="control-group">
  <button class="option-btn active" data-dough="#fcdbac">regular</button>
  <button class="option-btn" data-dough="#4e321d">chocolate</button>
  <button class="option-btn" data-dough="#8c211d">red velvet</button>
  </div>
</div>
<div id="time-wasted">time wasted since you opened this website: 00:00:00</div>
<!-- le toppings -->
<script>
const container = document.getElementById('donut-container');
const frostingMain = document.getElementById('frosting-main');
const colors = ['#f7f4f4', '#fff562', '#2799d8', '#39a126', '#e7484c', '#ff741a', '#402626', '#ff4fa7',];
let currentShape = 'long';
document.querySelectorAll('[data-frosting]').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('[data-frosting]').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    frostingMain.setAttribute('fill', btn.dataset.frosting);
  });
});
document.querySelectorAll('[data-shape]').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('[data-shape]').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentShape = btn.dataset.shape;
  });
});
container.addEventListener('click', (e) => {
  const rect = container.getBoundingClientRect();
  const x = e.clientX - rect.left;
  const y = e.clientY - rect.top;
  const centerX = rect.width / 2;
  const centerY = rect.height / 2;
  const dx = x - centerX;
  const dy = y - centerY;
  const distance = Math.sqrt(dx * dx + dy * dy);
  const holeRadius = rect.width * 0.11;
  if (distance < holeRadius) {
    return;
  }
  const sprinkle = document.createElement('div');
  sprinkle.className = currentShape === 'dot' ? 'sprinkle dot' : 'sprinkle';
  sprinkle.style.left = `${x}px`;
  sprinkle.style.top = `${y}px`;
  sprinkle.style.background = colors[Math.floor(Math.random() * colors.length)];
  if (currentShape === 'long') {
    sprinkle.style.transform = `rotate(${Math.random() * 360}deg)`;
  }
  container.appendChild(sprinkle);
});

const startTime = Date.now();
const timeWastedEl = document.getElementById('time-wasted');

function updateTimer() {
  const elapsedMs = Date.now() - startTime;
  const totalSeconds = Math.floor(elapsedMs / 1000);
  const hours = Math.floor(totalSeconds / 3600);
  const minutes = Math.floor((totalSeconds % 3600) / 60);
  const seconds = totalSeconds % 60;

  const pad = (num) => String(num).padStart(2, '0');
  timeWastedEl.textContent = `time wasted since you opened this website: ${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
}

setInterval(updateTimer, 1000);

const donut = document.getElementById('donut');

document.querySelectorAll('[data-dough]').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('[data-dough]').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    donut.style.background = btn.dataset.dough;
  });
});
</script>
</body>
</html>
