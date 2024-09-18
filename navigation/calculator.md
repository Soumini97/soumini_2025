---
layout: page
title: Calculator
permalink: /calculator/
---
<style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #F4F4F4;
        }
        .calculator {
            width: 300px;
            padding: 20px;
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
        }
        .display {
            width: 100%;
            height: 50px;
            background-color: #333;
            color: #fff;
            text-align: right;
            font-size: 2rem;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
        }
        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }
        button {
            padding: 20px;
            font-size: 1.2rem;
            background-color: #E3879E;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:active {
            background-color: #ccc;
        }
        .operator {
            background-color: #E3879E;
            color: white;
        }
        .operator:active {
            background-color: #E3879E;
        }
    </style>
<body>
<div class="calculator">
        <div class="display" id="display">0</div>
        <div class="buttons">
            <button onclick="clearDisplay()">C</button>
            <button onclick="appendToDisplay('(')">&#40;</button>
            <button onclick="appendToDisplay(')')">&#41;</button>
            <button class="operator" onclick="appendToDisplay('/')">/</button>
<button onclick="appendToDisplay('7')">7</button>
            <button onclick="appendToDisplay('8')">8</button>
            <button onclick="appendToDisplay('9')">9</button>
            <button class="operator" onclick="appendToDisplay('*')">*</button>
<button onclick="appendToDisplay('4')">4</button>
            <button onclick="appendToDisplay('5')">5</button>
            <button onclick="appendToDisplay('6')">6</button>
            <button class="operator" onclick="appendToDisplay('-')">-</button>
<button onclick="appendToDisplay('1')">1</button>
            <button onclick="appendToDisplay('2')">2</button>
            <button onclick="appendToDisplay('3')">3</button>
            <button class="operator" onclick="appendToDisplay('+')">+</button>
<button onclick="appendToDisplay('0')">0</button>
            <button onclick="appendToDisplay('.')">.</button>
            <button onclick="calculateResult()">=</button>
        </div>
    </div>
<script>
        let display = document.getElementById('display');
        // Function to append values to the display
        function appendToDisplay(value) {
            if (display.innerHTML === "0") {
                display.innerHTML = value;
            } else {
                display.innerHTML += value;
            }
        }
        // Function to clear the display
        function clearDisplay() {
            display.innerHTML = "0";
        }
        // Function to calculate the result
        function calculateResult() {
            try {
                display.innerHTML = eval(display.innerHTML);
            } catch (error) {
                display.innerHTML = "Error";
            }
        }
    </script>
</body>
