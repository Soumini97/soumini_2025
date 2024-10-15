---
layout: base
title: Soumini's Page
description: Home Page
image: /images/mario_animation.png
hide: true
---
<!-- Liquid:  statements -->

<!--- Concatenation of site URL to frontmatter image  --->
{% assign sprite_file = site.baseurl | append: page.image %}
<!--- Has is a list variable containing mario metadata for sprite --->
{% assign hash = site.data.mario_metadata %}  
<!--- Size width/height of Sprit images --->
{% assign pixels = 256 %}

<!--- HTML for page contains <p> tag named "Mario" and class properties for a "sprite"  -->

<p id="mario" class="sprite"></p>
  
<!--- Embedded Cascading Style Sheet (CSS) rules, 
        define how HTML elements look 
--->
<style>

  /*CSS style rules for the id and class of the sprite...
  */
  .sprite {
    height: {{pixels}}px;
    width: {{pixels}}px;
    background-image: url('{{sprite_file}}');
    background-repeat: no-repeat;
  }

  /*background position of sprite element
  */
  #mario {
    background-position: calc({{animations[0].col}} * {{pixels}} * -1px) calc({{animations[0].row}} * {{pixels}}* -1px);
  }
</style>

<!--- Embedded executable code--->
<script>
  ////////// convert YML hash to javascript key:value objects /////////

  var mario_metadata = {}; //key, value object
  {% for key in hash %}  
  
  var key = "{{key | first}}"  //key
  var values = {} //values object
  values["row"] = {{key.row}}
  values["col"] = {{key.col}}
  values["frames"] = {{key.frames}}
  mario_metadata[key] = values; //key with values added

  {% endfor %}

  ////////// game object for player /////////

  class Mario {
    constructor(meta_data) {
      this.tID = null;  //capture setInterval() task ID
      this.positionX = 0;  // current position of sprite in X direction
      this.currentSpeed = 0;
      this.marioElement = document.getElementById("mario"); //HTML element of sprite
      this.pixels = {{pixels}}; //pixel offset of images in the sprite, set by liquid constant
      this.interval = 100; //animation time interval
      this.obj = meta_data;
      this.marioElement.style.position = "absolute";
    }

    animate(obj, speed) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;

      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        this.marioElement.style.left = `${this.positionX}px`;

        this.positionX += speed;
        frame = (frame + 1) % obj.frames;

        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }

    startWalking() {
      this.stopAnimate();
      this.animate(this.obj["Walk"], 3);
    }

    startRunning() {
      this.stopAnimate();
      this.animate(this.obj["Run1"], 6);
    }

    startPuffing() {
      this.stopAnimate();
      this.animate(this.obj["Puff"], 0);
    }

    startCheering() {
      this.stopAnimate();
      this.animate(this.obj["Cheer"], 0);
    }

    startFlipping() {
      this.stopAnimate();
      this.animate(this.obj["Flip"], 0);
    }

    startResting() {
      this.stopAnimate();
      this.animate(this.obj["Rest"], 0);
    }

    stopAnimate() {
      clearInterval(this.tID);
    }
  }

  const mario = new Mario(mario_metadata);

  ////////// event control /////////

  window.addEventListener("keydown", (event) => {
    if (event.key === "ArrowRight") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.startWalking();
        } else if (mario.currentSpeed === 3) {
          mario.startRunning();
        }
      }
    } else if (event.key === "ArrowLeft") {
      event.preventDefault();
      if (event.repeat) {
        mario.stopAnimate();
      } else {
        mario.startPuffing();
      }
    }
  });

  //touch events that enable animations
  window.addEventListener("touchstart", (event) => {
    event.preventDefault(); // prevent default browser action
    if (event.touches[0].clientX > window.innerWidth / 2) {
      // move right
      if (currentSpeed === 0) { // if at rest, go to walking
        mario.startWalking();
      } else if (currentSpeed === 3) { // if walking, go to running
        mario.startRunning();
      }
    } else {
      // move left
      mario.startPuffing();
    }
  });

  //stop animation on window blur
  window.addEventListener("blur", () => {
    mario.stopAnimate();
  });

  //start animation on window focus
  window.addEventListener("focus", () => {
     mario.startFlipping();
  });

  //start animation on page load or page refresh
  document.addEventListener("DOMContentLoaded", () => {
    // adjust sprite size for high pixel density devices
    const scale = window.devicePixelRatio;
    const sprite = document.querySelector(".sprite");
    sprite.style.transform = `scale(${0.2 * scale})`;
    mario.startResting();
  });

</script>

## Intro
My journey starts here. Hi my name is Soumini.

<center>
<html>
<img src="https://images.ctfassets.net/o78em1y1w4i4/LHN0F94cNFCx1drEcfcsY/984e632abf38018f2a6ab22c4b61fdc6/teaser-book-with-heart-pages.jpg?fm=webp&w=1160&q=75" alt="Book" width="600" height="400" title="books" />
<p>I'm a huge bookworm. Me + Book + Quiet = Perfect Day</p>

<div style="border: 2px solid #001f3f; padding: 20px; max-width: 800px; margin: 0 auto; background-color: #001f3f;">
    <h2 style="text-align: center; color: white;">Sprint 1 Accomplishments</h2>
    <div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 20px;">
        <!-- Individual Button Boxes -->
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="https://soumini97.github.io/soumini_2025/emoji/" target="_blank"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
               Emojis Notebook
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/snake/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Snake Game
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/calculator/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Calculator
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/notebooks/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Jupyter Notebooks hacks
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/cookie/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Cookie Clicker Game
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/guessnumber/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Guess the Number Game
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/bookrecs/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Book Recs
            </a>
        </div>
        <div style="border: 1px solid #87CEFA; padding: 20px; border-radius: 5px; background-color: #87CEFA;">
            <a href="{{site.baseurl}}/itunesapi/"
               style="color: white; padding: 20px 40px; text-align: center; text-decoration: none; border-radius: 5px; font-size: 16px;">
                Itunes API
            </a>
        </div>
    </div>
</div>
    <style>
        /* Button style */
        .dropdown {
            position: relative;
            display: inline-block;
        }
        .dropdown-content {
            display: none;
            position: absolute;
            background-color: #f9f9f9;
            min-width: 160px;
            box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
            z-index: 1;
        }
        .dropdown-content a {
            color: black;
            padding: 12px 16px;
            text-decoration: none;
            display: block;
        }
        .dropdown-content a:hover {
            background-color: #f1f1f1;
        }
        .dropdown:hover .dropdown-content {
            display: block;
        }
        .dropdown-button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
        }
        .dropdown-button:hover {
            background-color: #45a049;
        }
    </style>
<body>

<div class="dropdown">
    <button class="dropdown-button">Dropdown</button>
    <div class="dropdown-content">
        <a href="https://www.barnesandnoble.com/">Barnes and Nobles</a>
        <a href="https://en.wikipedia.org/wiki/List_of_serial_killers_in_the_United_States">List of Serial Killers in the US</a>
        <a href="https://www.notion.so/">Student Planner</a>
    </div>
</div>





<center>
    <script>
        var obj = {
            name: "Soumini",
            age: 14,
            currentClasses: ["AP CSP", "AP Chemistry", "AP English Seminar", "AP Calc AB", "World History", "Honors Medical Interventions"],
            interests: ["reading", "writing", "painting", "solving math problems"],
            siblings: 1,  // Changed to number for math operations
            pets: "giant teddy bear"
        };
        // Manipulate the arrays
        // Adding a class
        obj.currentClasses.push("Biology");
        // Removing an interest
        obj.interests.splice(obj.interests.indexOf("painting"), 1);
        // Log the entire object
        console.log("Full object:", obj);
        // Log the specific changed keys
        console.log("Current Classes:", obj.currentClasses);
        console.log("Interests:", obj.interests);
        // Performing mathematical operations
        var yearsUntilAdult = 18 - obj.age; // Subtracting age from 18
        console.log(`Years until adulthood: ${yearsUntilAdult}`);
        var totalClasses = obj.currentClasses.length; // Number of current classes
        console.log(`Total classes: ${totalClasses}`);
        var petsCount = obj.siblings + 1; // Assuming 1 pet (change if needed)
        console.log(`Total pets (including 1 pet): ${petsCount}`);
        // Performing division
        var classesPerInterest = totalClasses / obj.interests.length;
        console.log(`Classes per interest: ${classesPerInterest.toFixed(2)}`);
        // Modulus operation (totalClasses % siblings)
        var classesModulusSiblings = totalClasses % obj.siblings;
        console.log(`Remainder of classes when divided by siblings: ${classesModulusSiblings}`);
        // Using typeof to determine types of fields
        console.log(`Type of name: ${typeof obj.name}`); // String
        console.log(`Type of age: ${typeof obj.age}`); // Number
        console.log(`Type of siblings: ${typeof obj.siblings}`); // Number
        console.log(`Type of currentClasses: ${typeof obj.currentClasses}`); // Object (arrays are objects)
    </script>

<script>
  var a = 5;
  var b = 10;
  if (a > b) {
    console.log("a is greater");
  } else if (a < b) {
    console.log("b is greater");
  } else {
    console.log("both are equal");
  }
  // Correcting the mathematical operations
  console.log(a + b);  // Adds a and b
  console.log(a - b);  // Subtracts b from a
  console.log(a * b);  // Multiplies a and b
  console.log(a / b);  // Divides a by b
</script>

