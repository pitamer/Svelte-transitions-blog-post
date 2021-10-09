<script>
  import { flip } from 'svelte/animate';
  import { crossfade } from 'svelte/transition';
  import { cubicOut } from 'svelte/easing';

  const DEFAULT_DURATION = 500;

  const [send, receive] = crossfade({duration: DEFAULT_DURATION, easing: cubicOut});

  export let options = [
    {name: "option 1", isSelected: false},
    {name: "option 2", isSelected: false},
    {name: "option 3", isSelected: false},
    {name: "option 4", isSelected: false},
    {name: "option 5", isSelected: false},
  ];

  const select = (name) => {
    options = options.map(option => ({...option, isSelected: option.name === name}));
  }

  $: availableOptions = options.filter(option => !option.isSelected)
</script>

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

  <div class="selected-item-slot">
    {#each options.filter(option => option.isSelected) as { name } (name)}
      <h1
        in:receive="{{key: name}}"
        out:send="{{key: name}}"
      >
        {name || ""}
      </h1>
    {/each}
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 420px;
    margin: 0 auto;
  }

  .selected-item-slot {
    display: grid;
    grid-template-columns: 1fr;
    place-items: center;
  }

  h1 {
    color: #ff3e00;
    font-size: 4em;
    font-weight: 100;
    display: inline-block;
    margin: 0;
    padding: 0;

    grid-row-start: 1;
    grid-column-start: 1;
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
