---
title: Snake Game
layout: default  
permalink: /snake_game
categories: [pbl]
tags: [javascript, style, controls, timers]
---
<html lang="en">
<head>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head>


<body>


<body>
<h1> Snake Game </h1>

Score: <div id="score">0</div>
<button onclick="new_game()" id="reload">New Game</button>
<canvas id="snakeboard" width="400" height="400"></canvas> <!-- sets up the board/dimension of the board -->

<!--formatting the format and design of the snake  board and the score -->
<!--future plans: add grids in the snakeboard-->
<style>
    #snakeboard {
        position: absolute;
        top: 40%;
        left: 69%;
        transform: translate(-50%, -50%);
    }
    #score {
        text-align: left;
        font-size: 100px;
    }
</style>
</body>
<!--code for the actual game-->
<script>
    const board_border = 'black'; /*setting up the color of the board and snake*/
    const board_background = "white";
    const snake_col = 'cyan';
    const snake_border = 'darkblue';
    let snake = [
        {x: 200, y: 200},
        {x: 190, y: 200},
        {x: 180, y: 200},
        {x: 170, y: 200},
        {x: 160, y: 200}
    ]
    let score = 0;
    let changing_direction = false;    /* True if changing direction*/
    let food_x;
    let food_y;
    let dx = 10; /* horizontal distance change */
    let dy = 0; /*vertical distance change */
    const snakeboard = document.getElementById("snakeboard"); /*get canvas element */
    const snakeboard_ctx = snakeboard.getContext("2d");
    /* start game*/
    main();
    gen_food();
    document.addEventListener("keydown", change_direction);
    // main function called repeatedly to keep the game running
    function main() { /*defining function main.*/
        if (has_game_ended()) return;
        changing_direction = false; /*if the player does not change the direction*/
        setTimeout(function onTick() {
            clear_board();
            drawFood();
            move_snake();
            drawSnake();
            // Repeat
            main();
        }, 100)
    }
    // draw a border around the canvas
    function clear_board() {
        //  Select the color to fill the drawing
        snakeboard_ctx.fillStyle = board_background;
        //  Select the colour for the border of the canvas
        snakeboard_ctx.strokestyle = board_border;
        // Draw a "filled" rectangle to cover end entire canvas
        snakeboard_ctx.fillRect(0, 0, snakeboard.width, snakeboard.height);
        // Draw a "border" around the entire canvas
        snakeboard_ctx.strokeRect(0, 0, snakeboard.width, snakeboard.height);
    }
    /*Draw the snake on the canvas*/
    function drawSnake() {
        snake.forEach(drawSnakePart)
    }
    /*creating food */
    function drawFood() {
        snakeboard_ctx.fillStyle = 'lightgreen';
        snakeboard_ctx.strokestyle = 'darkgreen';
        snakeboard_ctx.fillRect(food_x, food_y, 10, 10);
        snakeboard_ctx.strokeRect(food_x, food_y, 10, 10);
    }
    function drawSnakePart(snakePart) { /*draws the new boxes of snake, i.e. parts of snake*/
        snakeboard_ctx.fillStyle = snake_col; /*color of new part is the same as snake_col defined before*/
        snakeboard_ctx.strokestyle = snake_border; /*color of the border of new part is the same as snake_border defined before*/
        snakeboard_ctx.fillRect(snakePart.x, snakePart.y, 10, 10);
        /*fills in the new part with color + locate the color of the new part to the end of the snake*/
        snakeboard_ctx.strokeRect(snakePart.x, snakePart.y, 10, 10);
        /*draws a border aroudn the new part + locate the border to the end of the snake */
    }
    function new_game(){
        location.reload();
    }
    function has_game_ended() {
        for (let i = 4; i < snake.length; i++) {
            if (snake[i].x === snake[0].x && snake[i].y === snake[0].y) return true
            /* === means that the two things on both sides are of the same type and value*/
            /* && = and. both as to be true in order to return true */
        }
        const hitLeftWall = snake[0].x < 0;
        const hitRightWall = snake[0].x > snakeboard.width-20;
        const hitTopWall = snake[0].y < 0;
        const hitBottomWall = snake[0].y > snakeboard.height-20;
        return hitLeftWall || hitRightWall || hitTopWall || hitBottomWall
    }
    function random_food(min, max) {
        return Math.round((Math.random() * (max-min) + min) / 10) * 10;
    }
    function gen_food() {
        food_x = random_food(0, snakeboard.width - 10);
        /*generate random x-coord of food*/
        /*-10 so that it does not generate the x coord on where the wall is located at*/
        food_y = random_food(0, snakeboard.height - 10);
        /*generate random y-coord of food*/
        snake.forEach(function has_snake_eaten_food(part) {
            const has_eaten = part.x == food_x && part.y == food_y;
            if (has_eaten) gen_food();
        });
        /*if the food location is the snake location, generate new location*/
    }
    function change_direction(event) {
        const LEFT_KEY = 37;
        const RIGHT_KEY = 39;
        const UP_KEY = 38;
        const DOWN_KEY = 40;
        // Prevent the snake from reversing
        if (changing_direction) return;
        changing_direction = true; /*if the players changes direction */
        const keyPressed = event.keyCode;
        /*keyPressed stores information about which key is pressed*/
        const goingUp = dy === -10;
        const goingDown = dy === 10;
        const goingRight = dx === 10;
        const goingLeft = dx === -10;
        /*code above sets the value of each turn*/
        if (keyPressed === LEFT_KEY && !goingRight) {
            dx = -10;
            dy = 0;
        }
        /* if left key is pressed and the snake is not going right, then dx = -10 and dy = 0*/
        if (keyPressed === UP_KEY && !goingDown) {
            dx = 0;
            dy = -10;
        }
        /* if up key is pressed and the snake is not going down, then dx = 0 and dy = -10*/
        if (keyPressed === RIGHT_KEY && !goingLeft) {
            dx = 10;
            dy = 0;
        }
        /* if right key is pressed and the snake is not going left, then dx = 10 and dy = 0*/
        if (keyPressed === DOWN_KEY && !goingUp) {
            dx = 0;
            dy = 10;
        }
        /* if down key is pressed and the snake is not going up, then dx = 0 and dy = 10*/
    }
    function move_snake() {
        // Create the new Snake's head
        const head = {x: snake[0].x + dx, y: snake[0].y + dy};
        // Add the new head to the beginning of snake body
        snake.unshift(head);
        const has_eaten_food = snake[0].x === food_x && snake[0].y === food_y;
        if (has_eaten_food) {
            score += 10; /*add score*/
            document.getElementById('score').innerHTML = score; /*display score*/
            gen_food(); /*generate new food location*/
        } else {
            // Remove the last part of snake body
            snake.pop();
        }
    }
</script>





</body>


</html>