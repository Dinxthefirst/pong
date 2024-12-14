<script lang="ts">
  import { onMount, onDestroy } from "svelte";

  let leftScore = $state(0);
  let rightScore = $state(0);
  let leftPlayerY = $state(285);
  let rightPlayerY = $state(285);
  let ballX = $state(400);
  let ballY = $state(297);
  let ballDirection: number;

  const ballSpeed = 1;
  const playerSpeed = 1;
  let keysPressed: Record<string, boolean> = {
    w: false,
    s: false,
    ArrowUp: false,
    ArrowDown: false,
  };

  function handleKeyDown(event: KeyboardEvent) {
    keysPressed[event.key] = true;
  }

  function handleKeyUp(event: KeyboardEvent) {
    keysPressed[event.key] = false;
  }

  function updatePlayerPositions() {
    if (keysPressed.w) leftPlayerY -= playerSpeed;
    if (keysPressed.s) leftPlayerY += playerSpeed;
    if (keysPressed.ArrowUp) rightPlayerY -= playerSpeed;
    if (keysPressed.ArrowDown) rightPlayerY += playerSpeed;
    leftPlayerY = Math.max(0, Math.min(570, leftPlayerY));
    rightPlayerY = Math.max(0, Math.min(570, rightPlayerY));

    requestAnimationFrame(updatePlayerPositions);
  }

  function resetBall() {
    ballX = 400;
    ballY = 297;
    ballDirection = Math.random() * Math.PI * 2;
  }

  function updateBallPostion() {
    ballX = ballX + ballSpeed * Math.cos(ballDirection);
    ballY = ballY + ballSpeed * Math.sin(ballDirection);

    if (ballY < 0 || ballY > 594) {
      ballDirection = -ballDirection;
      ballDirection %= Math.PI * 2;
    }

    if (ballX < 70) {
      rightScore++;
      resetBall();
    }

    if (ballX > 730) {
      leftScore++;
      resetBall();
    }

    const isTouhingLeftPlayer =
      95 < ballX &&
      ballX < 100 &&
      ballY > leftPlayerY &&
      ballY < leftPlayerY + 30;

    const isTouchingRightPlayer =
      700 < ballX &&
      ballX < 705 &&
      ballY > rightPlayerY &&
      ballY < rightPlayerY + 30;

    if (isTouhingLeftPlayer || isTouchingRightPlayer) {
      ballDirection = Math.PI - ballDirection;
      ballDirection %= Math.PI * 2;
    }

    requestAnimationFrame(updateBallPostion);
  }

  onMount(() => {
    window.addEventListener("keydown", handleKeyDown);
    window.addEventListener("keyup", handleKeyUp);

    ballDirection = Math.random() * Math.PI * 2;

    requestAnimationFrame(updateBallPostion);
    requestAnimationFrame(updatePlayerPositions);
  });

  onDestroy(() => {
    window.removeEventListener("keydown", handleKeyDown);
    window.removeEventListener("keyup", handleKeyUp);
  });
</script>

<main>
  <div id="score-container">
    <div class="score">{leftScore}</div>
    <div class="score">{rightScore}</div>
  </div>
  <div id="game-container">
    <div id="game-area">
      <div class="player" style="left: 95px ; top: {leftPlayerY}px"></div>
      <div id="ball" style="left: {ballX}px ; top: {ballY}px"></div>
      <div class="player" style="left: 700px ; top: {rightPlayerY}px"></div>
    </div>
  </div>
</main>

<style>
  :global(*) {
    margin: 0;
    font-size: 48px;
    font-family: Arial, sans-serif;
    color: white;
  }

  :global(body) {
    background-color: #1a1a1a;
  }

  #score-container {
    display: flex;
    justify-content: center;
    gap: 200px;
  }

  #game-container {
    display: flex;
    justify-content: center;
  }

  #game-area {
    display: flex;
    border: 3px solid white;
    width: 800px;
    height: 600px;
  }

  #ball {
    position: relative;
    width: 6px;
    height: 6px;
    background-color: white;
  }

  .player {
    position: relative;
    width: 5px;
    height: 30px;
    background-color: white;
  }
</style>
