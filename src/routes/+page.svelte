<script lang="ts">
  import { Application, Sprite, Container, Graphics } from "pixi.js";
  import { onMount } from "svelte";

  let el: HTMLDivElement;

  onMount(() => {
    const app = new Application({
      background: "#fff",
      width: 1024,
      height: 682,
    });
    // eslint-disable-next-line no-underscore-dangle
    (window as any).__PIXI_APP__ = app;
    const outline = Sprite.from("outline.webp");
    const cursor = Sprite.from("verf-roller.png");
    cursor.visible = false;

    const original = Sprite.from("color.jpg");
    const container = new Container();
    container.addChild(original);
    const renderTexture = new Graphics();
    app.stage.addChild(outline);
    container.mask = renderTexture;
    app.stage.addChild(container);
    app.stage.addChild(cursor);
    const canvas = app.view as HTMLCanvasElement;
    el.appendChild(canvas);
    let painting: false | { x: number; y: number } = false;
    app.stage.eventMode = "dynamic";
    app.stage.on("pointerdown", (e) => {
      painting = { x: e.globalX, y: e.globalY };
    });
    app.stage.on("pointerup", () => {
      painting = false;
    });
    app.stage.on("pointermove", (e) => {
      const { globalX, globalY } = e;

      cursor.position.set(globalX - 100, globalY - 15);
      if (painting) {
        const steps =
          1 +
          Math.ceil(
            Math.max(
              Math.abs(globalX - painting.x),
              Math.abs(globalY - painting.y),
            ) / 15,
          );
        for (let i = 0; i < steps; i += 1) {
          renderTexture.beginFill(0x000000);
          const factor = i / steps;
          const invertedFactor = 1 - factor;
          renderTexture.drawRoundedRect(
            painting.x * factor + globalX * invertedFactor - 100,
            painting.y * factor + globalY * invertedFactor - 15,
            200,
            30,
            10,
          );
          renderTexture.endFill();
        }
        painting = { x: globalX, y: globalY };
      }
    });
    app.stage.on("pointerenter", () => {
      cursor.visible = true;
    });
    app.stage.on("pointerleave", () => {
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
<div class="wrapper" bind:this={el} />

<style>
  .wrapper {
    cursor: none;

    aspect-ratio: 1024/682;
    width: 1024px;
    max-width: 100%;
    margin: auto;
  }

  :global(canvas) {
    width: 100%;
    height: 100%;
  }
</style>
