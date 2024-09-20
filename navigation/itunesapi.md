---
layout: page
title: Itunes API
permalink: /itunesapi/
---

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>iTunes API Example</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        #results {
            margin-top: 20px;
        }
        .result-item {
            margin: 10px 0;
            border: 1px solid #ccc;
            padding: 10px;
            border-radius: 5px;
            display: flex;
            align-items: center;
        }
        .result-item img {
            margin-right: 15px;
        }
    </style>
</head>
<body>

<h1>iTunes Music Search</h1>
<input type="text" id="searchInput" placeholder="Search for music...">
<button id="searchButton">Search</button>

<div id="results"></div>

<script>
    document.getElementById('searchButton').addEventListener('click', function() {
        const searchTerm = document.getElementById('searchInput').value;
        const url = `https://itunes.apple.com/search?term=${encodeURIComponent(searchTerm)}&media=music`;

        fetch(url)
            .then(response => response.json())
            .then(data => {
                const resultsDiv = document.getElementById('results');
                resultsDiv.innerHTML = ''; // Clear previous results

                if (data.results.length === 0) {
                    resultsDiv.innerHTML = '<p>No results found.</p>';
                    return;
                }

                data.results.forEach(item => {
                    const itemDiv = document.createElement('div');
                    itemDiv.className = 'result-item';
                    itemDiv.innerHTML = `
                        <img src="${item.artworkUrl100}" alt="${item.trackName}" width="100">
                        <div>
                            <strong>${item.trackName}</strong> by ${item.artistName}<br>
                            <a href="${item.trackViewUrl}" target="_blank">Listen</a><br>
                            <audio controls>
                                <source src="${item.previewUrl}" type="audio/m4a">
                                Your browser does not support the audio tag.
                            </audio>
                        </div>
                    `;
                    resultsDiv.appendChild(itemDiv);
                });
            })
            .catch(error => {
                console.error('Error fetching data:', error);
                document.getElementById('results').innerHTML = '<p>Error fetching data. Please try again.</p>';
            });
    });
</script>

</body>

