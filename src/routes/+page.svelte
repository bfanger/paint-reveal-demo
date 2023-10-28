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
    canvas.addEventListener("mousemove", (e) => {
      const { offsetX, offsetY } = e;
      renderTexture.beginFill(0xff00ff);
      renderTexture.drawRect(offsetX - 100, offsetY - 15, 200, 30);
      renderTexture.endFill();
      cursor.x = offsetX - 100;
      cursor.y = offsetY - 15;
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
