<script lang="ts" context="module">
  export type { Placement } from '@popperjs/core'
</script>

<script lang="ts">
  import { createPopper, Instance, Placement } from '@popperjs/core'
  import { onMount } from 'svelte'

  export let placement: Placement = 'auto'
  export let text: string | null = null
  export let boundary: HTMLElement | string = 'clippingParents'
  export let offset: number = 4

  let trigger: HTMLSpanElement | null = null
  let hint: HTMLSpanElement | null = null
  let instance: Instance | null = null
  let dataShow = false
  let id = ''

  $: if (hint && trigger) {
    if (instance) instance.destroy()
    instance = createPopper(trigger, hint, {
      placement,
      modifiers: [
        {
          name: 'offset',
          options: {
            offset: [0, offset],
          },
        },
        {
          name: 'flip',
          options: {
            boundary,
          },
        },
      ],
    })
  }

  function show() {
    console.debug('show', hint)
    dataShow = true
    instance?.update()
  }

  function hide() {
    dataShow = false
  }

  onMount(() => {
    id = Math.random().toString(16).slice(2) // Random id for the tooltip
    const showEvents = ['mouseenter', 'focus']
    const hideEvents = ['mouseleave', 'blur']
    showEvents.forEach((event) => trigger?.addEventListener(event, show, false))
    hideEvents.forEach((event) => trigger?.addEventListener(event, hide, false))
    return () => {
      showEvents.forEach((event) => trigger?.removeEventListener(event, show, false))
      hideEvents.forEach((event) => trigger?.removeEventListener(event, hide, false))
    }
  })
</script>

<div bind:this={trigger} aria-describedby={id}>
  <slot />
</div>
<div bind:this={hint} {id} data-show={dataShow} role="tooltip" class="svelte-hint-tooltip">
  {#if text}
    <span>{text}</span>
  {:else}
    <slot name="hint" />
  {/if}
</div>

<style>
  .svelte-hint-tooltip {
    visibility: hidden;
  }

  .svelte-hint-tooltip[data-show='true'] {
    visibility: visible;
  }

  span {
    display: inline-block;
    background-color: #111;
    color: #fff;
    padding: 0.125rem 0.25rem;
    font-size: 0.8rem;
    width: max-content;
    border-radius: 0.25rem;
  }
</style>
