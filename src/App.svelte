<script lang="ts">
  import Game from "./lib/AsteroidsGame.svelte";
</script>

<main>
  <h1>Asteroids</h1>

  <div class="card">
    <Game />
  </div>

  <p class="read-the-docs">
    Click <a
      href="https://github.com/sveltejs/kit#readme"
      target="_blank"
      rel="noreferrer">guide</a
    >, to learn how to play.
  </p>
</main>

<style>
  .read-the-docs {
    color: #888;
  }
</style>
