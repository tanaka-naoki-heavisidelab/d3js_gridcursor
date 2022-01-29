<script lang="ts">
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let grid_sizes = [12, 24, 36, 48, 60];
  let grid_id = 2;
  let grid_length = grid_sizes.length - 1;
  let line_thick = 1;
  let grid_color = "#dddddd";
  let line_color = "#222222";

  $: gridform = {
    size: grid_sizes[grid_id] + "px",
    line: grid_sizes[grid_id] - line_thick + "px",
    color_g: grid_color,
    color_l: line_color,
  };

  $: cssVarStyles = Object.entries(gridform)
    .map(([key, value]) => `--${key}:${value}`)
    .join(";");

  let _svg: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  let svg = _svg as unknown | SVGElement;
  let _cur: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  onMount(() => {
    _svg = d3.select("svg");
    _cur = _svg.append("circle");
    _cur
      .attr("cx", "1000px")
      .attr("cy", "1000px")
      .attr("r", "4px")
      .attr("class", "circle")
      .attr("fill", "blue")
      .attr("id", "circle0");
  });

  function handlemousemove(evt) {
    d3.select(this).style("cursor", "crosshair");
    let x = Math.round(evt.offsetX / grid_sizes[grid_id]) * grid_sizes[grid_id];
    let y = Math.round(evt.offsetY / grid_sizes[grid_id]) * grid_sizes[grid_id];
    _cur.attr("cx", x + "px").attr("cy", y + 1 + "px");
  }
</script>

<div id="toolbar">
  <label for="gridsize"
    >グリッドサイズ
    <input
      type="range"
      bind:value={grid_id}
      min="0"
      max={grid_length}
      id="gridsize"
    />
  </label>
</div>

<div id="schematics" style={cssVarStyles}>
  <div class="grid" />
  <svg class="circuit" bind:this={svg} on:mousemove={handlemousemove} />
</div>

<style lang="scss">
  #toolbar {
    input[type="range"] {
      width: 10em;
      margin: 1em;
      vertical-align: middle;
    }
  }

  #schematics {
    position: relative;
    .grid {
      position: absolute;
      top: 0px;
      left: 0px;
      width: 600px;
      height: 600px;
      border: solid 1px var(--color_l);
      background-image: linear-gradient(
          0deg,
          transparent var(--line),
          var(--color_l) var(--size)
        ),
        linear-gradient(
          90deg,
          transparent var(--line),
          var(--color_l) var(--size)
        );
      background-color: var(--color_g);
      background-size: var(--size) var(--size);
    }
    .circuit {
      position: absolute;
      top: 0px;
      left: 0px;
      width: 600px;
      height: 600px;
    }
  }
</style>
