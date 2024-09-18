---
layout: page
title: Cookie Clicker
permalink: /cookie/
---
<!-- Cookie Clicker Game -->
<div id="cookie-game-container" style="text-align: center; margin-top: 20px;">
  <img id="cookie" src="{{site.baseurl}}/images/cookie.png" alt="Cookie" style="cursor: pointer;" width="400px" height="450px">
  <p>Cookies clicked: <span id="counter">0</span></p>
  <audio id="cookie-sound" src="sound/cookie.mp3" preload="auto"></audio>
</div>

<script>
  let counter = 0;
  let cookieSound = new Audio('../sound/cookie.mp3')
  document.getElementById('cookie').addEventListener('click', function() {
    counter++;
    document.getElementById('counter').textContent = counter;
    cookieSound.play();
  });
</script>


