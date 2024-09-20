---
layout: page
title: Guess The Number
permalink: /guessnumber/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guess the Number Game</title>
    <style>
        body { font-family: Arial, sans-serif; }
        #result { margin-top: 20px; }
    </style>
</head>
<body>
    <h1>Guess the Number!</h1>
    <p>Guess a number between 1 and 100:</p>
    <input type="number" id="guessInput" />
    <button id="submitGuess">Submit Guess</button>
    <div id="result"></div>
    <button id="resetGame" style="display:none;">Play Again</button>

    <script>
        let randomNumber = Math.floor(Math.random() * 100) + 1;
        let attempts = 0;

        const resultDiv = document.getElementById('result');
        const guessInput = document.getElementById('guessInput');
        const submitGuessButton = document.getElementById('submitGuess');
        const resetButton = document.getElementById('resetGame');

        submitGuessButton.addEventListener('click', checkGuess);
        resetButton.addEventListener('click', resetGame);

        function checkGuess() {
            const userGuess = Number(guessInput.value);
            attempts++;

            if (userGuess < 1 || userGuess > 100) {
                resultDiv.textContent = 'Please enter a number between 1 and 100.';
            } else if (userGuess < randomNumber) {
                resultDiv.textContent = 'Too low! Try again.';
            } else if (userGuess > randomNumber) {
                resultDiv.textContent = 'Too high! Try again.';
            } else {
                resultDiv.textContent = `Congratulations! You've guessed the number ${randomNumber} in ${attempts} attempts.`;
                submitGuessButton.disabled = true;
                resetButton.style.display = 'block';
            }
        }

        function resetGame() {
            randomNumber = Math.floor(Math.random() * 100) + 1;
            attempts = 0;
            resultDiv.textContent = '';
            guessInput.value = '';
            submitGuessButton.disabled = false;
            resetButton.style.display = 'none';
        }
    </script>
</body>
</html>
