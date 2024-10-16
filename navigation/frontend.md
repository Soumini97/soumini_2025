---
layout: page
title: Frontend
permalink: /frontend/
---

<html>

<!-- first information -->
<div>
<center>
    <!-- notice how tags can be put INSIDE eachother -->
    <p>Some fun links</p><br><br>
    <button onclick="window.location.href='https://poway.instructure.com';" >Canvas</button><br><br>
    <a href="https://www.spanishdict.com/translate/la%20m%C3%BAsica%20cubana" target="_blank">
    <button>Spanish Dictionary</button><br><br>
    </a>
    <!-- notice how tags can be put INSIDE eachother -->
    <a href="https://poway.instructure.com/courses/160449">AP english seminar course page</a><br><br>
    <a href="https://nighthawkcoders.github.io/portfolio_2025/frontend/basics/playground">random page in case you're bored</a><br><br>
    <p>Yay thanks for looking!</p>
    <center>
<div>

Link Changer:
<html>
<body>
    <a id="linkTitle" href="https://poway.instructure.com">Original Title</a>
    <button id="changeLinkButton">Click me!</button>
    <script>
        let clickCount = 0;
        const links = [
            "https://poway.instructure.com",
            "https://www.youtube.com/",
            "https://en.wikipedia.org/wiki/List_of_serial_killers_in_the_United_States",
            "https://www.spanishdict.com/"
        ];
        let currentLinkIndex = 0;
        function changeLink() {
            clickCount++;
            if (clickCount === 3) {
                // Change the link and update the innerHTML
                currentLinkIndex = (currentLinkIndex + 1) % links.length; // Cycle through links
                document.getElementById("linkTitle").innerHTML = "Switched to Link " + (currentLinkIndex + 1);
                document.getElementById("linkTitle").href = links[currentLinkIndex];
                clickCount = 0; // Reset the count
            }
        }
        // Add the click event to the button
        document.getElementById("changeLinkButton").onclick = changeLink;
    </script>
</body>
</html>


