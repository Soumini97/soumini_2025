---
layout: page
title: Snake
permalink: /snake/
---
<style>
    body {
        background: linear-gradient(135deg, #F9A8D4, #D4A9F9);
        font-family: 'Arial', sans-serif;
        color: #333;
        text-align: center;
        margin: 0;
        padding: 0;
    }
    .container {
        padding: 20px;
        max-width: 500px;
        margin: auto;
        background-color: #F9F5F7;
        border-radius: 15px;
        box-shadow: 0px 4px 20px rgba(0, 0, 0, 0.1);
    }
    .wrap {
        margin-left: auto;
        margin-right: auto;
    }
    canvas {
        display: none;
        border-style: solid;
        border-width: 10px;
        border-color: #F9A8D4;
        border-radius: 10px;
    }
    canvas:focus {
        outline: none;
    }

    /* Styling for the score display */
    header {
        font-size: 24px;
        margin-bottom: 20px;
    }

    /* Menu and buttons */
    #menu {
        font-size: 18px;
        color: #333;
    }
    #menu a, #gameover a, #setting a {
        display: block;
        font-size: 20px;
        margin-top: 10px;
        padding: 10px;
        background-color: #D4A9F9;
        color: white;
        border-radius: 5px;
        transition: background-color 0.3s;
    }
    #menu a:hover, #gameover a:hover, #setting a:hover {
        background-color: #F9A8D4;
        cursor: pointer;
    }
    #menu p, #gameover p, #setting p {
        font-size: 18px;
        margin: 10px 0;
    }

    /* Game Over screen */
    #gameover {
        display: none;
    }

    /* Settings */
    #setting {
        display: none;
    }

    #setting input {
        display: none;
    }
    #setting label {
        cursor: pointer;
        padding: 5px;
        background-color: #D4A9F9;
        border-radius: 5px;
    }
    #setting input:checked + label {
        background-color: #F9A8D4;
    }
</style>

<div class="container">
    <header>
        <p>Snake score: <span id="score_value">0</span></p>
    </header>
    <div class="container">
        <!-- Main Menu -->
        <div id="menu" class="py-4 text-light">
            <p>Welcome to Snake, press <span style="background-color: #F9A8D4; color: #FFFFFF">space</span> to begin</p>
            <a id="new_game" class="link-alert">New Game</a>
            <a id="setting_menu" class="link-alert">Settings</a>
        </div>
        <!-- Game Over -->
        <div id="gameover" class="py-4 text-light">
            <p>Game Over, press <span style="background-color: #F9A8D4; color: #FFFFFF">space</span> to try again</p>
            <a id="new_game1" class="link-alert">New Game</a>
            <a id="setting_menu1" class="link-alert">Settings</a>
        </div>
        <!-- Settings Screen -->
        <div id="setting" class="py-4 text-light">
            <p>Settings Screen, press <span style="background-color: #F9A8D4; color: #FFFFFF">space</span> to go back to playing</p>
            <a id="new_game2" class="link-alert">New Game</a>
            <br>
            <p>Speed:
                <input id="speed1" type="radio" name="speed" value="120" checked/>
                <label for="speed1">Slow</label>
                <input id="speed2" type="radio" name="speed" value="75"/>
                <label for="speed2">Normal</label>
                <input id="speed3" type="radio" name="speed" value="35"/>
                <label for="speed3">Fast</label>
            </p>
            <p>Wall:
                <input id="wallon" type="radio" name="wall" value="1" checked/>
                <label for="wallon">On</label>
                <input id="walloff" type="radio" name="wall" value="0"/>
                <label for="walloff">Off</label>
            </p>
        </div>
    </div>
</div>

<script>
(function(){
    /* Attributes of Game */
    /////////////////////////////////////////////////////////////
    // Canvas & Context
    const canvas = document.getElementById("snake");
    const ctx = canvas.getContext("2d");
    // HTML Game IDs
    const SCREEN_SNAKE = 0;
    const screen_snake = document.getElementById("snake");
    const ele_score = document.getElementById("score_value");
    const speed_setting = document.getElementsByName("speed");
    const wall_setting = document.getElementsByName("wall");

    // HTML Screen IDs (div)
    const SCREEN_MENU = -1, SCREEN_GAME_OVER=1, SCREEN_SETTING=2;
    const screen_menu = document.getElementById("menu");
    const screen_game_over = document.getElementById("gameover");
    const screen_setting = document.getElementById("setting");

    // HTML Event IDs (a tags)
    const button_new_game = document.getElementById("new_game");
    const button_new_game1 = document.getElementById("new_game1");
    const button_new_game2 = document.getElementById("new_game2");
    const button_setting_menu = document.getElementById("setting_menu");
    const button_setting_menu1 = document.getElementById("setting_menu1");

    // Game Control
    const BLOCK = 10;   // size of block rendering
    let SCREEN = SCREEN_MENU;
    let snake;
    let snake_dir;
    let snake_next_dir;
    let snake_speed;
    let food = {x: 0, y: 0};
    let score;
    let wall;

    /* Display Control */
    let showScreen = function(screen_opt){
        SCREEN = screen_opt;
        switch(screen_opt){
            case SCREEN_SNAKE:
                screen_snake.style.display = "block";
                screen_menu.style.display = "none";
                screen_setting.style.display = "none";
                screen_game_over.style.display = "none";
                break;
            case SCREEN_GAME_OVER:
                screen_snake.style.display = "block";
                screen_menu.style.display = "none";
                screen_setting.style.display = "none";
                screen_game_over.style.display = "block";
                break;
            case SCREEN_SETTING:
                screen_snake.style.display = "none";
                screen_menu.style.display = "none";
                screen_setting.style.display = "block";
                screen_game_over.style.display = "none";
                break;
        }
    }

    window.onload = function(){
        // HTML Events to Functions
        button_new_game.onclick = function(){newGame();};
        button_new_game1.onclick = function(){newGame();};
        button_new_game2.onclick = function(){newGame();};
        button_setting_menu.onclick = function(){showScreen(SCREEN_SETTING);};
        button_setting_menu1.onclick = function(){showScreen(SCREEN_SETTING);};
        // speed
        setSnakeSpeed(150);
        for(let i = 0; i < speed_setting.length; i++){
            speed_setting[i].addEventListener("click", function(){
                for(let i = 0; i < speed_setting.length; i++){
                    if(speed_setting[i].checked){
                        setSnakeSpeed(speed_setting[i].value);
                    }
                }
            });
        }
        // wall setting
        setWall(1);
        for(let i = 0; i < wall_setting.length; i++){
            wall_setting[i].addEventListener("click", function(){
                for(let i = 0; i < wall_setting.length; i++){
                    if(wall_setting[i].checked){
                        setWall(wall_setting[i].value);
                    }
                }
            });
        }
        // activate window events
        window.addEventListener("keydown", function(evt) {
            if(evt.code === "Space" && SCREEN !== SCREEN_SNAKE)
                newGame();
        }, true);
    }

    let mainLoop = function(){
        let _x = snake[0].x;
        let _y = snake[0].y;
        snake_dir = snake_next_dir;   // read async event key
        // Direction 0 - Up, 1 - Right, 2 - Down, 3 - Left
        if(snake_dir == 0) _y -= 1;      // up
        if(snake_dir == 1) _x += 1;      // right
        if(snake_dir == 2) _y += 1;      // down
        if(snake_dir == 3) _x -= 1;      // left
        // move snake by adding a new head
        snake.unshift({x: _x, y: _y});

        // check if snake hits wall
        if(wall && (_x < 0 || _x >= canvas.width / BLOCK || _y < 0 || _y >= canvas.height / BLOCK)){
            return gameOver();
        }

        // check if snake eats itself
        if(snake.slice(1).some(s => s.x === _x && s.y === _y)){
            return gameOver();
        }

        // check if snake eats food
        if(_x === food.x && _y === food.y){
            score += 1;
            foodEaten();
        } else {
            snake.pop();   // move snake without growing
        }
        
        // drawing
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        // draw snake
        for(let i = 0; i < snake.length; i++){
            ctx.fillStyle = "rgb(179, 255, 136)";
            ctx.fillRect(snake[i].x * BLOCK, snake[i].y * BLOCK, BLOCK, BLOCK);
        }
        // draw food
        ctx.fillStyle = "rgb(255, 136, 136)";
        ctx.fillRect(food.x * BLOCK, food.y * BLOCK, BLOCK, BLOCK);

        // Update the score
        ele_score.innerText = score;
    }

    let foodEaten = function(){
        food.x = Math.floor(Math.random() * (canvas.width / BLOCK - 1));
        food.y = Math.floor(Math.random() * (canvas.height / BLOCK - 1));
    }

    let setSnakeSpeed = function(speed){
        snake_speed = speed;
        if(snake_speed == 120) snake_speed = 150;    // slower
        if(snake_speed == 75) snake_speed = 75;      // faster
        if(snake_speed == 35) snake_speed = 35;      // faster 
    }
    let setWall = function(wall_mode){
        wall = (wall_mode == 1) ? true : false;
    }

    let newGame = function(){
        score = 0;
        foodEaten();
        snake = [{x: 3, y: 3}];
        snake_dir = 1;  // initial direction
        snake_next_dir = 1;
        showScreen(SCREEN_SNAKE);
        mainLoop();
        setInterval(mainLoop, snake_speed);
    }
    let gameOver = function(){
        showScreen(SCREEN_GAME_OVER);
    }
})();
</script>

