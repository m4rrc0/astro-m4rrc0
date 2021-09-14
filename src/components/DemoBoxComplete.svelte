<script>
  import Paragraph from "../partials/styleguide/Paragraph.svelte";

  export let style = "";
  export let i = 1;
  export let palette = i;
  export let variation = "classic";
</script>

<div
  class={`box stack color-palette-${palette} color-palette-variation-${variation}`}
  {style}
>
  <p class="h3">Palette {palette} - Variation {variation}</p>
  <Paragraph />
  <p>Don't forget to select some text to test colors</p>
  <code class="code-block">
    <pre>
      <p class="h2">An h2 fox jumps over the fence or whatever</p><p class="h3">An h3 fox jumps over the fence or whatever</p><p class="h4">An h4 fox jumps over the fence or whatever</p>
    </pre>
  </code>
  <blockquote>
    Alway pay attention to the coupling you are introducing in your code base
    when using specific tools. Ask yourself: "if I want to change this piece,
    how easy will it be?"
  </blockquote>
  <div class="cluster overflow">
    <ul class="no">
      <li>
        <button>Magnificent</button>
      </li>
      <li>
        <button class="color-palette-variation-contrast"> Wondeful </button>
      </li>
      <li>
        <button class="color-palette-variation-funky">Marvelous</button>
      </li>
      <li>
        <button class="color-palette-variation-funky-contrast">
          Glorious
        </button>
      </li>
    </ul>
  </div>
</div>
