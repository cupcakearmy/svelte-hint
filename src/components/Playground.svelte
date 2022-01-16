<script lang="ts">
  import Hint from '../lib/Hint.svelte'
  import interact from 'interactjs'
  import { onMount } from 'svelte'

  const directions = ['auto', 'left', 'top', 'bottom', 'right']
  const aligns = ['start', 'center', 'end']

  let offset = 4
  let text = 'Here is a very secret hint!'
  let direction = 'bottom'
  let align = 'center'

  $: placement = `${direction}-${align}`.replace('-center', '') as any
  $: auto = (placement.startsWith('auto') && (placement.includes('-') ? placement.split('-')[1] : true)) as any

  let knob: HTMLDivElement

  onMount(() => {
    interact(knob).draggable({
      inertia: true,
      modifiers: [
        interact.modifiers.restrictRect({
          restriction: '.container',
        }),
      ],
      listeners: {
        move(event: Interact.DragEvent) {
          var target = event.target

          var x = parseFloat(target.getAttribute('data-x') || '0') + event.dx
          var y = parseFloat(target.getAttribute('data-y') || '0') + event.dy

          target.style.top = `calc(50% + ${y}px)`
          target.style.left = `calc(50% + ${x}px)`

          target.setAttribute('data-x', x.toString())
          target.setAttribute('data-y', y.toString())
        },
      },
    })
  })
</script>

<p>Move around the button and change the properties.</p>

<h3>Options</h3>

<div class="mb-3">
  <label for="text" class="form-label">Text</label>
  <input type="text" class="form-control" id="text" bind:value={text} />
</div>

<div class="mb-1">
  <i>Placement: </i> <code>{placement}</code>
</div>
<div class="flex-centered mb-3">
  <div class="btn-group" role="group" aria-label="Tooltip direction">
    {#each directions as d}
      <button class="btn btn-{direction === d ? 'primary' : 'light'}" on:click={() => (direction = d)}>{d}</button>
    {/each}
  </div>
  <div class="btn-group" role="group" aria-label="Tooltip alignment">
    {#each aligns as a}
      <button class="btn btn-{align === a ? 'primary' : 'light'}" on:click={() => (align = a)}>{a}</button>
    {/each}
  </div>
</div>

<div class="mb-3">
  <label for="offset" class="form-label"><i>Offset:</i></label>
  <code>{offset}</code>
  <input type="range" class="form-range" id="offset" min="0" max="32" step="1" bind:value={offset} />
</div>

<div class="container bg-light">
  <div bind:this={knob}>
    <Hint {placement} {offset} {text} {auto}>
      <button class="btn btn-success drag">Hover me!</button>
      <div slot="hint">Test</div>
    </Hint>
  </div>
</div>

<p>
  Positioning gets recalculated <b>when hovering</b>, not while moving the button around.
</p>

<style>
  .container {
    width: 100%;
    aspect-ratio: 2/1;
    position: relative;
    overflow: hidden;
  }

  .container > div {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .drag {
    cursor: move;
  }

  button {
    text-transform: capitalize;
    width: max-content;
  }
</style>
