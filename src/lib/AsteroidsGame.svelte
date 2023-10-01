<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let hits = 0;
  let misses = 0;
  $: posX = 140;
  $: posY = 130;

  $: ship = {
    x: posX,
    y: posY,
    width: 20,
    height: 20,
    speed: 10,
    draw: () => {
      ctx.beginPath();
      ctx.rect(ship.x, ship.y, ship.width, ship.height);
      ctx.fillStyle = "black";
      ctx.fill();
      ctx.closePath();
    },
  };

  $: bullets = [] as Array<{
    x: number;
    y: number;
    width: number;
    height: number;
  }>;

  $: asteroids = [] as Array<{
    x: number;
    y: number;
    radius: number;
    speed: number;
  }>;

  $: weapon = {
    speed: 5,
    fire: () => {
      const bullet = {
        x: ship.x + ship.width / 2,
        y: ship.y,
        width: 2,
        height: 10,
      };
      bullets.push(bullet);
    },
    draw: () => {
      for (const [i, bullet] of bullets.entries()) {
        bullet.y -= weapon.speed;
        ctx.beginPath();
        ctx.rect(bullet.x, bullet.y, bullet.width, bullet.height);
        ctx.fillStyle = "red";
        ctx.fill();
        ctx.closePath();
        if (bullet.y < 0) {
          bullets.splice(i, 1);
        }
      }
    },
  };

  $: asteroidField = {
    draw: () => {
      for (const [i, asteroid] of asteroids.entries()) {
        asteroid.y += asteroid.speed;
        ctx.beginPath();
        ctx.arc(asteroid.x, asteroid.y, asteroid.radius, 0, Math.PI * 2);
        ctx.fillStyle = "black";
        ctx.fill();
        ctx.closePath();
        // Check if any bullets hit the asteroid
        for (const [j, bullet] of bullets.entries()) {
          if (
            bullet.x > asteroid.x - asteroid.radius &&
            bullet.x < asteroid.x + asteroid.radius &&
            bullet.y > asteroid.y - asteroid.radius &&
            bullet.y < asteroid.y + asteroid.radius
          ) {
            // Remove the asteroid and bullet when hit
            asteroids.splice(i, 1);
            bullets.splice(j, 1);

            // Award points
            hits += 1; // Increase the score by 1 for each hit
          }
        }
        if (asteroid.y > ctx.canvas.height) {
          asteroid.x = Math.random() * ctx.canvas.width;
          asteroid.y = -Math.random() * ctx.canvas.height;
          misses += 1;
        }
      }
    },
    create: () => {
      for (let index = 0; index < 5; index++) {
        const asteroid = {
          x: Math.random() * ctx.canvas.width,
          y: 0,
          radius: Math.random() * 6 + 3,
          speed: Math.random() * 2 + 1,
        };
        asteroids.push(asteroid);
      }
    },
  };

  function render() {
    ctx.clearRect(0, 0, ctx.canvas.clientWidth, ctx.canvas.clientHeight);
    ship.draw();
    weapon.draw();
    asteroidField.draw();
    requestAnimationFrame(render);
  }

  function handleKeyPress(
    e: KeyboardEvent & {
      currentTarget: EventTarget & HTMLCanvasElement;
    }
  ) {
    const key = e.key;
    switch (key) {
      case "d":
        if (posX < ctx.canvas.width - ship.width) posX += ship.speed;
        break;
      case "a":
        if (posX > 0) posX -= ship.speed;
        break;
      case "w":
        if (posY > 0) posY -= ship.speed;
        break;
      case "s":
        if (posY < ctx.canvas.height - ship.height) posY += ship.speed;
        break;
      case " ":
        weapon.fire();
        break;
      default:
        console.log(`${key} key not supported`);
        break;
    }
  }

  onMount(() => {
    ctx = canvas.getContext("2d")!;
    ctx.canvas.focus();
    asteroidField.create();
    render();
  });
</script>

<p>Hits: {hits}</p>
<p>Misses: {misses}</p>
<canvas
  tabindex="0"
  bind:this={canvas}
  on:keypress={(e) => handleKeyPress(e)}
/>

<style>
  canvas {
    width: 500px;
    height: 300px;
    outline: 1px solid gray;
  }
</style>
