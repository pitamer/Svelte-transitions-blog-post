Transitioning multiple Svelte elements on the same spot

// Explain the problem

## Step 1: Our example app

```sveltehtml
<script>
  export let options = [
    {name: "option 1", isSelected: false},
    {name: "option 2", isSelected: false},
    {name: "option 3", isSelected: false},
    {name: "option 4", isSelected: false},
    {name: "option 5", isSelected: false},
  ]

  const select = (name) => {
    options = options.map(option => ({...option, isSelected: option.name === name}));
  }

  $: availableOptions = options.filter(option => !option.isSelected)
</script>

<main>
  <ul>
    {#each availableOptions as { name } (name)}
      <li on:click={() => select(name)}>
        {name}
      </li>
    {/each}
  </ul>

  <h1>{options.find(option => option.isSelected)?.name || ""}</h1>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 420px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    font-size: 4em;
    font-weight: 100;
    display: inline-block;
    margin: 0;
    padding: 0;
  }

  ul {
    display: flex;
    list-style: none;
    justify-content: space-between;
    padding: 0;
    margin: 0 0 75px 0;
  }

  ul li {
    cursor: pointer;
  }
</style>
```

// gif

Take this:

```sveltehtml
<h1>{options.find(option => option.isSelected)?.name || ""}</h1>
```

And change it to this: 

```sveltehtml
{#each options.filter(option => option.isSelected) as {name}}
  <h1>{name || ""}</h1>
{/each}
```

The app should look exactly the same, but under the hood, we're fooling Svelte into treating these like 2 blah blah (self check)

## Step 2: Adding the Transitions

Now, the fun part.

3 imports:

```sveltehtml
import { flip } from 'svelte/animate';
import { crossfade } from 'svelte/transition';
import { cubicOut } from 'svelte/easing';
```

Changes to the main element

```sveltehtml
<main>
  <ul>
    {#each availableOptions as { name } (name)}
      <li
      on:click={() => select(name)}
      in:receive="{{key: name}}"
      out:send="{{key: name}}"
      animate:flip="{{duration: DEFAULT_DURATION}}"
    >
      {name}
      </li>
    {/each}
  </ul>

  {#each options.filter(option => option.isSelected) as { name } (name)}
    <h1
      in:receive="{{key: name}}"
      out:send="{{key: name}}"
    >
      {name || ""}
    </h1>
  {/each}
</main>
```

## Step 3: Fixing the Problem





// [!] (plugin svelte) ValidationError: An element that uses the animate directive must be the immediate child of a keyed each block