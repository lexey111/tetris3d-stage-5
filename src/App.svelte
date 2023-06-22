<script lang="ts">
  import GameOverBanner from "./banners/GameOverBanner.svelte";
  import PauseBanner from "./banners/PauseBanner.svelte";
  import StartBanner from "./banners/StartBanner.svelte";
  import Keys from "./components/Keys.svelte";
  import Next from "./components/Next.svelte";
  import Scene from "./components/Scene.svelte";
  import type { Array10, TGameField } from "./components/types";

  let started = false;
  let paused = false;
  let gameOver = false;

  let level = 1;
  let score = 0;

  function isBanner() {
    return !started || paused || gameOver;
  }

  const figures = "SZLJITO".split("");
  let figure = "";

  setInterval(() => {
    figure = figures[Math.floor(Math.random() * figures.length)];
  }, 500);

  setInterval(() => {
    fillField(-1);
    printGame(GameField);
  }, 1000);

  // create an empty game field
  const GameField: TGameField = new Array<Array10>(24) as TGameField;
  // and fill it with random values (num === -1)
  fillField(-1);

  printGame(GameField);

  function fillField(num) {
    for (let row = 0; row < GameField.length; row++) {
      if (!GameField[row]) {
        GameField[row] = new Array<number>(10) as Array10;
      }
      for (let col = 0; col < GameField[row].length; col++) {
        // num can be a static value, or if it is negative, random value 0..4
        GameField[row][col] =
          num >= 0
            ? num
            : Math.random() > 0.7
            ? Math.floor(Math.random() * 4)
            : 0;
      }
    }
  }

  function printGame(GameField: TGameField) {
    for (let i = GameField.length - 1; i >= 0; i--) {
      let s = i.toString().padStart(2, " ") + " ";

      for (let j = 0; j < GameField[i].length; j++) {
        if (GameField[i][j] === 0) {
          s += ".";
        }
        if (GameField[i][j] === 1) {
          s += "F";
        }
        if (GameField[i][j] === 2) {
          s += "S";
        }
        if (GameField[i][j] === 3) {
          s += "X";
        }
      }
      console.log(s);
    }
  }
</script>

<main>
  <div id="main-container">
    <div id="scene-container">
      <Scene {GameField} />
    </div>

    <div id="info-container">
      <Next {figure} />
      <div id="score-container">
        <h1>100</h1>
        <h2>level 2</h2>
      </div>
    </div>
  </div>
  <div id="help-container">
    <Keys />
  </div>
</main>

{#if isBanner()}
  <div id="banner">
    {#if !started}
      <StartBanner />
    {/if}
    {#if paused}
      <PauseBanner currentLevel={level} currentScore={score} />
    {/if}
    {#if gameOver}
      <GameOverBanner finalLevel={level} finalScore={score} />
    {/if}
  </div>
{/if}

<style>
  #main-container {
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    align-content: center;
    justify-content: center;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    bottom: 100px;
  }
  #scene-container {
    width: auto;
    aspect-ratio: 10 / 24;
    height: 100%;
    align-self: center;
    position: relative;
    margin-left: 60px;
  }
  #info-container {
    height: 100%;
    width: 120px;
    text-align: center;
  }
  #info-container h1,
  #info-container h2 {
    font-weight: normal;
    margin: 0;
    padding: 0;
  }
  #help-container {
    display: flex;
    justify-content: center;
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
  }
  #banner {
    position: fixed;
    z-index: 1;

    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
  }
</style>
