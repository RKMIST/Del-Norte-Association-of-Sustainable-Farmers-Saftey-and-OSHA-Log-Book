---
toc: true
comments: false
hide: false
layout: post
title: Ideation Check 2
---

# Login 

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

### Assets and Scenes
For the game to function properly, it is imperative that the assets are preloaded so that all assets display properly when being shown to the end user. For this, Phaser.Loader.LoaderPlugin must be used in order to preload the assets. This will be done by using this.load() within the Phaser scene. 

Here's some example JS code that shows what is being discussed:
```
preload() {
    // images :)
    this.load.image('avatar', 'https://example.com/avatar.png');
    // sprites that Ryan will be designing.
    this.load.spritesheet('player', 'path/to/player.png', { frameWidth: 32, frameHeight: 48 }); 
    // Load other assets from URLs
}
```
Using this methodology, we can make sure the game is ready to display all the sprites and the user has a smooth user experience.

In Phaser.js, a "scene" is a fundamental concept that represents a specific state or section of a game. Scenes are used to organize and separate different parts of your game, such as the main menu, gameplay, settings, and so on. Each scene can have its own logic, assets, and functionality. Think as scenes as "screens" in a sense. There needs to be various scenes for the menus and gameplay, and we're going to need to constantly swap between screens.

Here is some example code showcasing this:
```
// Create instances of scenes
const mainMenuScene = new MainMenuScene();
const gameplayScene = new GameplayScene();

// Start the main menu scene
this.scene.add('MainMenuScene', mainMenuScene);
this.scene.start('MainMenuScene');

// Switch from main menu to game scene
this.scene.stop('MainMenuScene');
this.scene.start('GameplayScene')
```

*** Data can be passed between scenes. *** This allows us to transfer data about the user and their save as they are navigating menus and playing the game. Data can be passed along to another scene as so, assume the code is a continuation of the code block above:
```
// Stop gameplay scene and switch data to the next scene
this.scene.stop('GameplayScene');
this.scene.start('NextScene', { score: 100 });

``` 

# Animation

# Storing Data

# Research Methods and Real World Sources


