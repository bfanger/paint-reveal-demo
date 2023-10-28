<script lang="ts">
  import * as PIXI from "pixi.js";
  import { onMount } from "svelte";

  let el: HTMLDivElement;

  onMount(() => {
    const app = new PIXI.Application({
      background: "#fff",
      width: 1024,
      height: 682,
    });
    const outline = PIXI.Sprite.from("outline.webp");
    const cursor = PIXI.Sprite.from("verf-roller.png");
    cursor.visible = false;

    const original = PIXI.Sprite.from("color.jpg");
    const container = new PIXI.Container();
    container.addChild(original);
    const renderTexture = new PIXI.Graphics();
    app.stage.addChild(outline);
    container.mask = renderTexture;
    app.stage.addChild(container);
    app.stage.addChild(cursor);
    const canvas = app.view as HTMLCanvasElement;
    el.appendChild(canvas);
    let painting: false | { x: number; y: number } = false;
    canvas.addEventListener("mousedown", (e) => {
      painting = { x: e.offsetX, y: e.offsetY };
    });
    canvas.addEventListener("mouseup", (e) => {
      painting = false;
    });
    canvas.addEventListener("mousemove", (e) => {
      const { offsetX, offsetY } = e;

      cursor.x = offsetX - 100;
      cursor.y = offsetY - 15;
      if (painting) {
        const steps =
          1 +
          Math.ceil(
            Math.max(
              Math.abs(offsetX - painting.x),
              Math.abs(offsetY - painting.y),
            ) / 5,
          );
        for (let i = 0; i < steps; i += 1) {
          renderTexture.beginFill(0x000000);
          const factor = i / steps;
          const invertedFactor = 1 - factor;
          renderTexture.drawRoundedRect(
            painting.x * factor + offsetX * invertedFactor - 100,
            painting.y * factor + offsetY * invertedFactor - 15,
            200,
            30,
            10,
          );
          renderTexture.endFill();
        }
        painting = { x: offsetX, y: offsetY };
      }
    });
    canvas.addEventListener("mouseenter", () => {
      cursor.visible = true;
    });
    canvas.addEventListener("mouseleave", () => {
      cursor.visible = false;
    });
    return () => {
      app.destroy();
      canvas.remove();
    };
  });
</script>

<svelte:head>
  <title>Paint demo page</title>
</svelte:head>
<div class="canvas" bind:this={el}></div>

<style>
  .canvas {
    width: 1024px;
    margin: auto;
    cursor: none;
  }
</style>
