---
toc: true
comments: false
layout: post
type: plan
title: Ideation Check 2.5
---

# General Overview 
With our new project we will explore a 2d pixel art based game/simulation. Where we will allow the user to explore every aspect of sustainable agriculture from dealing with core principles of farm design to reduce erosion or water use, creating irrigation systems that reduce evaporation loss, the main principles of Integrated Pest Management. While understanding the outcomes of not following those principles like damage to local environments and ecology. All of this will focusing on the costs of these issues. Leveraging our team's passion for environmental issues, while also using the artistic skills from our chief asset designer Ryan. There will be elements were the user will be able to see and and choose which of these many avenues to venture down and understand what it is to be a farmer, and most importantly an ecologically conscious farmer.


## Login 
Example of the Login page:

<img width="956" alt="image" src="https://github.com/RKMIST/RKMIST-DevLogs/assets/20897400/602f2d4e-27d8-40ec-8a09-78b3711d5e9b">

Code for the login page implements thymeleaf in the javascript but is not functional yet because no backend code has been created yet.

```
<body>
    <h2>Login</h2>
    <form th:action="@{/login}" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required th:field="${user.username}" />

        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required th:field="${user.password}" />

        <button type="submit">Login</button>
    </form>
</body>
```

# Grid Layout for Game Map

Example of the grid layout for the farm fields:

<img width="947" alt="image" src="https://github.com/RKMIST/RKMIST-DevLogs/assets/20897400/7930bee7-d845-4f29-b689-9b140834f32f">


The code is based on a 2d array abstracted by nested for loops as you can see below, in the full project this would be converted to a 3d array so that each tile can store its own data and easily be retrieved. The frontend will run using html canvas which is ideal for making 2d graphics.

```
        let gridArray = [
            [0, 0, 0, 0],
            [1, 1, 0, 2],
            [1, 1, 2, 2],
            [1, 2, 2, 2]
        ];

        // Canvas setup
        const canvas = document.getElementById('gridCanvas');
        const ctx = canvas.getContext('2d');

        // Tile size
        const tileSize = 50;

        // Map array values to colors
        const colorMap = {
            0: 'pink',
            1: 'blue',
            2: 'yellow'
            // Add more colors as needed
        };

        // Draw grid based on the 2D array
        function drawGrid() {
            for (let row = 0; row < gridArray.length; row++) {
                for (let col = 0; col < gridArray[row].length; col++) {
                    const x = col * tileSize;
                    const y = row * tileSize;

                    // Get color based on array value
                    const color = colorMap[gridArray[row][col]];

                    // Set color
                    ctx.fillStyle = color || 'black';

                    // Draw rectangle
                    ctx.fillRect(x, y, tileSize, tileSize);
                }
            }
        }

        // Function to handle mouse clicks
        function handleClick(event) {
            const mouseX = event.clientX - canvas.getBoundingClientRect().left;
            const mouseY = event.clientY - canvas.getBoundingClientRect().top;

            const clickedCol = Math.floor(mouseX / tileSize);
            const clickedRow = Math.floor(mouseY / tileSize);

            // Change the color of the clicked tile
            if (gridArray[clickedRow] && gridArray[clickedRow][clickedCol] !== undefined) {
                gridArray[clickedRow][clickedCol] = (gridArray[clickedRow][clickedCol] + 1) % Object.keys(colorMap).length;
                drawGrid();
            }
        }

        // Add click event listener to the canvas
        canvas.addEventListener('click', handleClick);

        // Call the drawGrid function to initially draw the grid
        drawGrid();
```

[Diagram ](https://docs.google.com/drawings/d/1SoLCICa7x4YviAG47N6djyN_XNhxIQS1QHPJHtAcLQc/edit)

# Phaser.js Framework
### What is Phaser.js?
Phaser is a Javascript framework that is the industry standard for 2D game programming. We will be utilizing the canvas feature to create our grid/game map, as illustrated by Ryan in the above sections. 

### Assets and JWT
For the game to function properly, it is imperative that the assets are preloaded so that all assets 

# Animation

# Storing Data

# Research Methods and Real World Sources


# Databases

## General Implementation
- As the game is a single person farming simulation we don't need to maintain stores of information on the server side to handle the information regrading the game state.
- The only data stored within the SQL on the backend will be general metrics about each player's stats to display on a leaderboard as previously mentioned.
- This allows us to move the database into the browser through the indexedDB property as our form of persistent game data storage.
- With the asynchronous nature of the indexedDB we will need to use promises to handle the data retrieval and storage not interrupting the game loop.

## IndexedDB

```javascript
// We will begin through creating an instance of the db object
let db;

// then we will need to open the database that we will be using to store the use's game data
const request = indexedDB.open('gameData', 1); // the 1 is the version number of the database this is an advantage as we can implement a rollback system if needed

// We would also need to define the event handlers for successful and failed opening of the db
// This will allow for operations that can occur if the db opens or doesn't open properly such as fall back methods we have yet to implement

request.addEventListener('success', event => {
    db = event.target.result; // this will set the db variable to the result of the request which is the contents of the database which we now can edit 
});

request.addEventListener('error', event => {
    console.log('Error opening database');
});



// We will create an update event listener to handle adding elements to the database
request.addEventListener("upgradeneeded", (e) => {
  // Grab a reference to the opened database
  db = e.target.result;

  // Create an objectStore aka a table in our database to store notes and an auto-incrementing key
  const objectStore = db.createObjectStore("gamestate_os", {
    keyPath: "id",
    autoIncrement: true,
  });

  // Define what data items the objectStore will contain this can be the attributes of the player's farm to reference later and build the game from 
  // These values will be determined from the game selections from the game loop
  objectStore.createIndex("irrigationMethod", "drip", { unique: false });
  objectStore.createIndex("farmStyle", "terraced", { unique: false });

  console.log("Database setup complete");
});
```
- In this demo we will have the database configured to store some basic farm info however we would also need to define some more additional content
- We need to create a method to add our data to the database to add the new save states to the database

```javascript
// Define the addData() function to be able to write and simply add data to our database 
// Allowing us to add the data simply and easily we can also create effects that will happen when there are updates
function addData(e) {
    e.preventDefault();
    const newItem = { title: titleInput.value, body: bodyInput.value };
    const transaction = db.transaction(["gamestate_os"], "readwrite");
    const objectStore = transaction.objectStore("gamestate_os");
    const addRequest = objectStore.add(newItem);

    addRequest.addEventListener("success", () => {
        Console.log("Data added to the database successfully");
    });

    transaction.addEventListener("complete", () => {
        console.log("Transaction completed: database modification finished.");
        displayData();
    });

    transaction.addEventListener("error", () =>
        console.log("Transaction not opened due to error"),
    );
}
```
- This will allow us to create a more general methods to create the save state mechanics for the individual players where they once upon closing the browser will still be able to play and continue to access their prior states. 

