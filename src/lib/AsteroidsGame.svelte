<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  $: posX = 140;
  $: posY = 130;

  $: ship = {
    x: posX,
    y: posY,
    width: 20,
    height: 20,
    speed: 20,
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

  function render() {
    ctx.clearRect(0, 0, ctx.canvas.clientWidth, ctx.canvas.clientHeight);
    ship.draw();
    weapon.draw();
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
        if (posX < 280) posX += ship.speed;
        break;
      case "a":
        if (posX > 0) posX -= ship.speed;
        break;
      case "w":
        if (posY > 0) posY -= ship.speed;
        break;
      case "s":
        if (posY < 130) posY += ship.speed;
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
    render();
  });
</script>

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
