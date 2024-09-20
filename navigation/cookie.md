---
layout: page
title: Cookie Clicker
permalink: /cookie/
---
<!-- Cookie Clicker Game -->
<div id="cookie-game-container" style="text-align: center; margin-top: 20px;">
  <img id="cookie" src="{{site.baseurl}}/images/cookie.png" alt="Cookie" style="cursor: pointer;" width="400px" height="450px">
  <p>Cookies clicked: <span id="counter">0</span></p>
  <p>Workers: <span id="worker-count">0</span></p>
  <p>Cookies per second: <span id="cookies-per-second">0</span></p>
  <button id="buy-worker" disabled>Buy Worker (25 Clicks)</button>
  <audio id="cookie-sound" src="../sound/cookie.mp3" preload="auto"></audio>
</div>

<script>
  let counter = 0;
  let workerCount = 0;
  const cookiesPerSecondElement = document.getElementById('cookies-per-second');
  const workerCountElement = document.getElementById('worker-count');
  const buyWorkerButton = document.getElementById('buy-worker');
  const cookieSound = document.getElementById('cookie-sound');

  document.getElementById('cookie').addEventListener('click', function() {
    counter++;
    document.getElementById('counter').textContent = counter;
    
    // Play the sound
    cookieSound.currentTime = 0; // Reset sound to start
    cookieSound.play().catch(error => {
      console.error("Audio playback failed:", error);
    });

    // Enable the buy worker button if the player has 25 clicks
    if (counter >= 25) {
      buyWorkerButton.disabled = false;
    }
  });

  buyWorkerButton.addEventListener('click', function() {
    if (counter >= 25) {
      workerCount++;
      counter -= 25; // Deduct clicks for the worker
      document.getElementById('counter').textContent = counter;
      workerCountElement.textContent = workerCount;
      cookiesPerSecondElement.textContent = workerCount; // Each worker gives 1 cookie per second
    }

    // Disable the button if not enough clicks
    if (counter < 25) {
      buyWorkerButton.disabled = true;
    }
  });

  // Function to increment cookies per second
  setInterval(() => {
    counter += workerCount; // Increase the counter based on the number of workers
    document.getElementById('counter').textContent = counter;
  }, 1000); // Update every second
</script>
