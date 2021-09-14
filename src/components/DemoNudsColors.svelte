<script>
  import DemoBoxComplete from "./DemoBoxComplete.svelte";

  let userColors = [
    { name: "white", value: "#ffffff" },
    { name: "black", value: "#111111" },
    { name: "dark", value: "#333333" },
    { name: "grey", value: "#888888" },
    { name: "red", value: "#ff0000" },
    { name: "blue", value: "#0000ff" },
    { name: "green", value: "#00ff00" },
  ];

  $: userColorsString = userColors
    .map(({ name, value }) => `--color-${name}:${value};`)
    .join("");

  let paletteDefs = [
    {
      name: "1",
      def: [
        { paletteName: "neutral", colorName: "white" },
        { paletteName: "primary", colorName: "dark" },
        { paletteName: "secondary", colorName: "grey" },
      ],
    },
    {
      name: "2",
      def: [
        { paletteName: "neutral", colorName: "black" },
        { paletteName: "primary", colorName: "red" },
        { paletteName: "secondary", colorName: "green" },
      ],
    },
  ];

  $: paletteDefStrings = paletteDefs.map(({ name, def }) => ({
    name,
    def,
    string: def
      .map(
        ({ paletteName, colorName }) =>
          `--color-${paletteName}-palette: var(--color-${colorName});`
      )
      .join(""),
  }));

  let variationDefs = [
    {
      name: "classic",
      def: [
        { variationName: "neutral", paletteName: "neutral" },
        { variationName: "primary", paletteName: "primary" },
        { variationName: "secondary", paletteName: "secondary" },
      ],
    },
    {
      name: "contrast",
      def: [
        { variationName: "neutral", paletteName: "primary" },
        { variationName: "primary", paletteName: "neutral" },
        { variationName: "secondary", paletteName: "secondary" },
      ],
    },
    {
      name: "funky",
      def: [
        { variationName: "neutral", paletteName: "neutral" },
        { variationName: "primary", paletteName: "secondary" },
        { variationName: "secondary", paletteName: "primary" },
      ],
    },
    {
      name: "funky-contrast",
      def: [
        { variationName: "neutral", paletteName: "secondary" },
        { variationName: "primary", paletteName: "neutral" },
        { variationName: "secondary", paletteName: "primary" },
      ],
    },
  ];

  $: variationDefStrings = variationDefs.map(({ name, def }) => ({
    name,
    def,
    string: def
      .map(
        ({ variationName, paletteName }) =>
          `--color-${variationName}: var(--color-${paletteName}-palette);`
      )
      .join(""),
  }));

  let colorAssigns = {
    text: "primary",
    bg: "neutral",
    "selection-text": "neutral",
    "selection-bg": "secondary",
    "a-text": "text",
    "a-bg": "bg",
    "a_hover-text": "secondary",
    "a_hover-bg": "neutral",
    "button-text": "text",
    "button-bg": "bg",
    "button_hover-text": "secondary",
    "button_hover-bg": "bg",

    // TODO
    // "button_disabled-text": "secondary",
    // "button_disabled-bg": "bg",
    // "code-text": "bg",
    // "code-bg": "secondary",
    // "code-border": "primary",

    // fallbacks are probably fine
    border: "", // falls back to 'currentcolor'
    outline: "", // falls back to 'currentcolor'
    "text-decoration": "", // falls back to 'currentcolor'
    "text-emphasis": "", // falls back to 'currentcolor'
    caret: "", // falls back to 'currentcolor'
    "column-rule": "", // falls back to 'currentcolor'
  };

  $: colorAssignsString = Object.entries(colorAssigns)
    .map(
      ([property, assignation]) =>
        `--color-${property}: var(--color-${assignation});`
    )
    .join("");

  // $: console.log({ userColors, paletteDef });

  $: style = `
  /* nuds color system */

  /* The colors we want to use throughout the site */
  /* multiple color definitions like */
  /* --color-white: #fff; */
  ${userColorsString}

  /* color PALETTES */
  /* set initial palette values (same as .color-palette-1) */
  /* like */
  /* --color-neutral-palette: var(--color-white); */
  ${paletteDefStrings[0].string}
  /* set initial variation values (same as .color-palette-variation-classic) */
  /* like */
  /* --color-neutral: var(--color-neutral-palette); */
  ${variationDefStrings[0].string}
    
      /* say how the palette should be used */
      --color-text: var(--color-primary);
      --color-bg: var(--color-neutral);
      --color-selection-text: var(--color-neutral);
      --color-selection-bg: var(--color-secondary);
      --color-a-text: var(--color-text);
      --color-a-bg: var(--color-bg);
      --color-a_hover-text: var(--color-secondary);
      --color-a_hover-bg: var(--color-neutral);
      --color-button-text: var(--color-text);
      --color-button-bg: var(--color-bg);
      --color-button_hover-text: var(--color-secondary);
      --color-button_hover-bg: var(--color-bg);
      /* TODO */
      /* --color-button_disabled-text: var(--color-secondary);
      /* --color-button_disabled-bg: var(--color-bg); */
  
      /* --color-code-text: var(--color-bg); */
      /* --color-code-bg: var(--color-secondary); */
      /* --color-code-border: var(--color-primary); */
  ${colorAssignsString}

      `.replace(/(\r\n|\n|\r)/gm, "");

  //   let style = `

  //   /* Let's assign variables to every possible css color property */
  //   /* ATTENTION: this is also the defaults in case custom properties are not supported */
  //   color: #111;
  //   /* will be overridden if custom properties are supported */
  //   /* we provide a default value here too in case custom properties are supported but the variable is not set for some reason */
  //   color: var(--color-text, #111);
  //   background-color: var(--color-bg, transparent);
  //   /* other properties have a default of 'currentcolor' so we don't have to set a default value if we are fine with that */
  //   border-color: var(--color-border, currentcolor);
  //   outline-color: var(--color-outline, currentcolor);
  //   text-decoration-color: var(--color-text-decoration, currentcolor);
  //   text-emphasis-color: var(--color-text-emphasis, currentcolor);
  //   caret-color: var(--color-caret, currentcolor);
  //   column-rule-color: var(--color-column-rule, currentcolor);

  //   /* assign to all children of a block with a color palette defined */
  //   color: inherit;
  //   /* background-color: inherit; */
  //   border-color: inherit;
  //   outline-color: inherit;
  //   text-decoration-color: inherit;
  //   text-emphasis-color: inherit;
  //   caret-color: inherit;
  //   column-rule-color: inherit;
  // `;

  // $: boxStyle = `
  //   /* color PALETTES */
  // /* set initial palette values (same as .color-palette-1) */
  // /* like */
  // /* --color-neutral-palette: var(--color-white); */
  // ${paletteDefString}
  // /* set initial variation values (same as .color-palette-variation-classic) */
  // /* like */
  // /* --color-neutral: var(--color-neutral-palette); */
  // ${variationDefString}

  // `.replace(/(\r\n|\n|\r)/gm, "");

  const todoStyles = `
*:focus,
*[class^='color-palette-']:focus,
*[class^='color-palette-'] *:focus {
  outline-color: var(--color-outline, currentcolor);
}

*::selection,
*[class^='color-palette-']::selection,
*[class^='color-palette-'] *::selection {
  color: #fff;
  color: var(--color-selection-text, #fff);
  background-color: #000;
  background-color: var(--color-selection-bg, #000);
}

a,
a[class^='color-palette-'],
*[class^='color-palette-'] a {
  color: currentcolor;
  color: var(--color-a-text, #000);
  background-color: transparent;
  background-color: var(--color-a-bg, transparent);
}

a:hover,
a[class^='color-palette-']:hover,
*[class^='color-palette-'] a:hover {
  color: #888;
  color: var(--color-a_hover-text, #888);
  background-color: transparent;
  background-color: var(--color-a_hover-bg, transparent);
}

button,
.button,
button[class*='color-palette-'],
.button[class*='color-palette-'],
*[class*='color-palette-'] button,
*[class*='color-palette-'] .button {
  color: currentcolor;
  color: var(--color-button-text, #111);
  background-color: transparent;
  background-color: var(--color-button-bg, transparent);
  border-color: var(--color-button-border, currentcolor);
}

button:hover,
.button:hover,
button[class*='color-palette-']:hover,
.button[class*='color-palette-']:hover,
*[class*='color-palette-'] button:hover,
*[class*='color-palette-'] .button:hover {
  color: #888;
  color: var(--color-button_hover-text, #888);
  background-color: transparent;
  background-color: var(--color-button_hover-bg, transparent);
  border-color: var(--color-button_hover-border, currentcolor);
}

button:disabled,
.button:disabled,
button[class*='color-palette-']:disabled,
.button[class*='color-palette-']:disabled,
*[class*='color-palette-'] button:disabled,
*[class*='color-palette-'] .button:disabled {
  color: #999;
  color: var(--color-button_disabled-text, #999);
  background-color: #eee;
  background-color: var(--color-button_disabled-bg, #eee);
  border-color: var(--color-button_disabled-border, currentcolor);
}

tt,
tt[class^='color-palette-'],
*[class^='color-palette-'] tt,
code,
code[class^='color-palette-'],
*[class^='color-palette-'] code,
kbd,
kbd[class^='color-palette-'],
*[class^='color-palette-'] kbd,
pre,
pre[class^='color-palette-'],
*[class^='color-palette-'] pre,
samp,
samp[class^='color-palette-'],
*[class^='color-palette-'] samp {
  color: var(--color-code-text, currentcolor);
  background-color: var(--color-code-bg, transparent);
  border-color: var(--color-code-border, currentcolor);
}

/* End of variables assignation and default values */

/* color palette VARIATIONS */

/* ... */

button[class*='color-palette-'][class*='-contrast'] {
  /* we can manage exceptions on a variation basis if necessary
    or with more complex selectors */
  --color-button_hover-text: var(--color-bg);
  --color-button_hover-bg: var(--color-text);
  --color-button-border: var(--color-bg);
  --color-outline: var(--color-bg);
}
`;
</script>

<form>
  {#each userColors as color, index}
    <div class="cluster">
      <div>
        <label><input type="text" bind:value={color.name} /></label>
        <label><input type="color" bind:value={color.value} /></label>
      </div>
    </div>
  {/each}
  <br />
  {#each paletteDefs as palette, index}
    <label>Palette name: <input type="text" bind:value={palette.name} /></label>
    {#each palette.def as def, index}
      <div class="cluster">
        <div>
          <label><input type="text" bind:value={def.paletteName} /></label>
          <!-- <label><input type="text" bind:value={def.colorName} /></label> -->
          <!-- <select bind:value={def.colorName} /> -->
          <select bind:value={def.colorName}>
            {#each userColors as color}
              <option value={color.name}>
                {color.name}
              </option>
            {/each}
          </select>
        </div>
      </div>
    {/each}
  {/each}
  <br />
  {#each variationDefs as variation, index}
    <label
      >Variation name: <input type="text" bind:value={variation.name} /></label
    >
    {#each variation.def as def, index}
      <div class="cluster">
        <div>
          <label><input type="text" bind:value={def.variationName} /></label>
          <label><input type="text" bind:value={def.paletteName} /></label>
        </div>
      </div>
    {/each}
  {/each}
  <br />

  {#each paletteDefStrings as palette}
    <p class="h3">Palette {palette.name}</p>
    <div class="grid size-context-miniature" {style}>
      <div
        style="--width-column: calc(var(--max-width, var(--measure, 38rem)) / 2.1);"
      >
        {#each variationDefStrings as variation}
          <DemoBoxComplete
            palette={palette.name}
            variation={variation.name}
            style={`${palette.string}${variation.string}${colorAssignsString}`}
          />
        {/each}
      </div>
    </div>
  {/each}

  {#each Object.entries(colorAssigns) as [property, assignation]}
    <div class="cluster">
      <div>
        <label for={property}>{property}</label>
        <input type="text" bind:value={colorAssigns[property]} />
      </div>
    </div>
  {/each}
</form>

<style>
  input[type="text"] {
    width: 12ch;
  }
</style>
