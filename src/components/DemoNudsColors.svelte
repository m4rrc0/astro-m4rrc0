<script>
  import DemoBoxComplete from "./DemoBoxComplete.svelte";

  const removeIndexFromArray = (arr, i) =>
    arr.filter((element, index) => i !== index);

  const presets = {
    simple: {
      userColors: [
        { name: "light", value: "#ffffff" },
        { name: "dark", value: "#111111" },
        { name: "blue", value: "#0000ff" },
      ],
      paletteSlots: ["neutral", "primary", "secondary"],
      palettesNames: ["main"],
      palettesColors: [["light", "dark", "blue"]],
      variationsNames: ["classic", "contrast"],
      variationsCorrespondances: [
        ["neutral", "primary", "secondary"],
        ["primary", "neutral", "secondary"],
      ],
    },
    complete: {
      userColors: [
        { name: "white", value: "#ffffff" },
        { name: "black", value: "#111111" },
        { name: "dark", value: "#333333" },
        { name: "grey", value: "#888888" },
        { name: "red", value: "#ff0000" },
        { name: "blue", value: "#0000ff" },
        { name: "green", value: "#00ff00" },
      ],
      paletteSlots: ["neutral", "primary", "secondary"],
      palettesNames: ["1", "2"],
      palettesColors: [
        ["white", "dark", "grey"],
        ["black", "red", "green"],
      ],
      variationsNames: ["classic", "contrast", "funky", "funky-contrast"],
      variationsCorrespondances: [
        ["neutral", "primary", "secondary"],
        ["primary", "neutral", "secondary"],
        ["neutral", "secondary", "primary"],
        ["secondary", "neutral", "primary"],
      ],
      colorAssigns: {
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
      },
    },
  };
  const applyPreset = (presetName) => {
    const currentPreset = presets[presetName];
    if (!currentPreset) {
      error.log(`no preset defined with name ${presetName}`);
      return null;
    }
    userColors = currentPreset.userColors;
    paletteSlots = currentPreset.paletteSlots;
    palettesNames = currentPreset.palettesNames;
    palettesColors = currentPreset.palettesColors;
    variationsNames = currentPreset.variationsNames;
    variationsCorrespondances = currentPreset.variationsCorrespondances;
    colorAssigns = currentPreset.colorAssigns || colorAssigns;
  };

  // USER COLORS
  let userColors = presets.complete.userColors;

  const addUserColor = () => {
    userColors = [...userColors, { name: "white", value: "#ffffff" }];
  };
  const removeUserColor = ({ index, name }) => {
    const r = confirm(`Sure you want to remove color '${name}'?`);
    if (r == true) {
      userColors = removeIndexFromArray(userColors, index);
    }
  };

  $: userColorsString = userColors
    .map(({ name, value }) => `--color-${name}:${value};`)
    .join("");

  // PALETTE SLOTS
  let paletteSlots = presets.complete.paletteSlots;
  const addPaletteSlot = () => {
    paletteSlots = [...paletteSlots, "new"];
    palettesColors = palettesColors.map((paletteColors) => [
      ...paletteColors,
      "",
    ]);
    variationsCorrespondances = variationsCorrespondances.map(
      (variationCorrespondances) => [...variationCorrespondances, ""]
    );
  };
  const removePaletteSlot = ({ index, name }) => {
    const r = confirm(`Sure you want to remove the slot '${name}'?`);
    if (r == true) {
      paletteSlots = removeIndexFromArray(paletteSlots, index);
    }
  };

  // PALETTES DEFINITIONS
  let palettesNames = presets.complete.palettesNames;
  let palettesColors = presets.complete.palettesColors;
  // let paletteDefs = [
  //   {
  //     name: "1",
  //     def: [
  //       { slotName: "neutral", colorName: "white" },
  //       { slotName: "primary", colorName: "dark" },
  //       { slotName: "secondary", colorName: "grey" },
  //     ],
  //   },
  //   ...
  // ];
  $: paletteDefs = palettesNames.map((paletteName, indexOfPalette) => {
    const def = paletteSlots.map((slotName, indexOfSlot) => ({
      slotName,
      colorName: palettesColors[indexOfPalette][indexOfSlot],
    }));
    return {
      name: paletteName,
      def,
    };
  });

  const addPalette = () => {
    palettesNames = [...palettesNames, `${palettesNames.length + 1}`];
    palettesColors = [...palettesColors, ["", "", ""]];
  };
  const removePalette = ({ index, name }) => {
    const r = confirm(`Sure you want to remove the palette '${name}'?`);
    if (r == true) {
      palettesNames = removeIndexFromArray(palettesNames, index);
      palettesColors = removeIndexFromArray(palettesColors, index);
    }
  };

  $: paletteDefStrings = paletteDefs.map(({ name, def }) => ({
    name,
    def,
    string: def
      .map(
        ({ slotName, colorName }) =>
          `--color-${slotName}-palette: var(--color-${colorName});`
      )
      .join(""),
  }));

  // VARIATIONS DEFINITIONS
  let variationsNames = presets.complete.variationsNames;
  // correspondances against paletteSlots
  let variationsCorrespondances = presets.complete.variationsCorrespondances;
  // let variationDefs = [
  //   {
  //     name: "classic",
  //     def: [
  //       { slotName: "neutral", nameInVariation: "neutral" },
  //       { slotName: "primary", nameInVariation: "primary" },
  //       { slotName: "secondary", nameInVariation: "secondary" },
  //     ],
  //   },
  //   ...
  // ];
  $: variationDefs = variationsNames.map((variationName, indexOfVariation) => {
    const def = paletteSlots.map((slotName, indexOfSlot) => ({
      slotName,
      nameInVariation: variationsCorrespondances[indexOfVariation][indexOfSlot],
    }));
    return {
      name: variationName,
      def,
    };
  });

  const addVariation = () => {
    variationsNames = [...variationsNames, `${variationsNames.length + 1}`];
    variationsCorrespondances = [...variationsCorrespondances, ["", "", ""]];
  };
  const removeVariation = ({ index, name }) => {
    const r = confirm(`Sure you want to remove the variation '${name}'?`);
    if (r == true) {
      variationsNames = removeIndexFromArray(variationsNames, index);
      variationsCorrespondances = removeIndexFromArray(
        variationsCorrespondances,
        index
      );
    }
  };

  $: variationDefStrings = variationDefs.map(({ name, def }) => ({
    name,
    def,
    string: def
      .map(
        ({ slotName, nameInVariation }) =>
          `--color-${slotName}: var(--color-${nameInVariation}-palette);`
      )
      .join(""),
  }));

  let colorAssigns = presets.complete.colorAssigns;

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

<div class="stack gap-down-h1">
  <div>
    <p class="h2">Presets</p>
    <div class="cluster gap-down-h3">
      <div>
        {#each Object.keys(presets) as presetName}
          <button on:click={() => applyPreset(presetName)}>{presetName}</button>
        {/each}
      </div>
    </div>
  </div>

  <div>
    <p class="h2">Colors to be used</p>
    {#each userColors as color, index}
      <div class="cluster gap-down-h3">
        <div>
          <label><input type="text" bind:value={color.name} /></label>
          <label><input type="color" bind:value={color.value} /></label>
          <button on:click={() => removeUserColor({ index, name: color.name })}
            >( - )</button
          >
        </div>
      </div>
    {/each}
    <div class="">
      <button on:click={addUserColor}>Add a color</button>
    </div>
  </div>

  <div>
    <p class="h2">Color palette slots</p>
    {#each paletteSlots as slotName, index}
      <div class="cluster gap-down-h3">
        <div>
          <label><input type="text" bind:value={slotName} /></label>
          <button on:click={() => removePaletteSlot({ index, name: slotName })}
            >( - )</button
          >
        </div>
      </div>
    {/each}
    <div class="">
      <button on:click={addPaletteSlot}>Add a color slot in palette</button>
    </div>
  </div>

  <div>
    <p class="h2">Color palettes</p>
    {#each palettesNames as name, indexOfPalette}
      <label>Palette name: <input type="text" bind:value={name} /></label>
      <button on:click={() => removePalette({ index: indexOfPalette, name })}
        >( - )</button
      >
      {#each palettesColors[indexOfPalette] as colorName, indexOfColorInPalette}
        <div class="cluster gap-down-h3">
          <div>
            <span>{paletteSlots[indexOfColorInPalette]}</span>
            <select bind:value={colorName}>
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
    <div class="">
      <button on:click={addPalette}>Add a color palette</button>
    </div>
  </div>

  <div>
    <p class="h2">Color palette variations</p>
    <div class="grid gap-down-h8">
      <div
        style="--width-column: calc(var(--max-width, var(--measure, 38rem)) / 2.1);"
      >
        {#each variationsNames as variationName, indexOfVariation}
          <div class="box">
            <label
              >Variation name: <input
                type="text"
                bind:value={variationName}
              /></label
            >
            {#each variationsCorrespondances[indexOfVariation] as corr, indexOfSlotInVariation}
              <div class="cluster gap-down-h3">
                <div>
                  <span>{paletteSlots[indexOfSlotInVariation]}</span>
                  <select bind:value={corr}>
                    {#each paletteSlots as paletteSlot}
                      <option value={paletteSlot}>
                        {paletteSlot}
                      </option>
                    {/each}
                  </select>
                </div>
              </div>
            {/each}
            <button
              on:click={() =>
                removeVariation({
                  index: indexOfVariation,
                  name: variationName,
                })}>( - )</button
            >
          </div>
        {/each}
      </div>
    </div>
    <div class="">
      <button on:click={addVariation}>Add a palette variation</button>
    </div>
  </div>

  <div>
    {#each paletteDefStrings as palette}
      <p class="h3">Palette {palette.name}</p>
      <div class="grid size-context-miniature gap-down-h8" {style}>
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
  </div>

  <div>
    {#each Object.entries(colorAssigns) as [property, assignation]}
      <div class="cluster gap-down-h3">
        <div>
          <label for={property}>{property}</label>
          <input type="text" bind:value={colorAssigns[property]} />
        </div>
      </div>
    {/each}
  </div>
</div>

<style>
  input[type="text"] {
    width: 12ch;
  }
</style>
