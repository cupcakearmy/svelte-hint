<script lang="ts" context="module">
  import type { Boundary, Placement } from '@floating-ui/dom'
</script>

<script lang="ts">
  import { computePosition, flip, shift, offset as offsetMiddleware, autoPlacement } from '@floating-ui/dom'
  import { onMount } from 'svelte'

  export let placement: Placement | undefined = undefined
  export let text: string | null = null
  export let boundary: Boundary = 'clippingAncestors'
  export let offset: Parameters<typeof offsetMiddleware>[0] = 4
  export let auto: boolean | 'start' | 'end' = false

  let trigger: HTMLDivElement | null = null
  let hint: HTMLDivElement | null = null
  let dataShow = false
  let id = ''

  async function update() {
    if (!hint || !trigger) return

    const options = {
      placement: placement as Placement,
      middleware: auto
        ? [offsetMiddleware(offset), autoPlacement({ boundary, alignment: typeof auto === 'string' ? auto : null })]
        : [offsetMiddleware(offset), flip({ boundary }), shift({ boundary, padding: 4 })],
    }
    const { x, y } = await computePosition(trigger, hint, options)
    Object.assign(hint!.style, {
      left: `${x}px`,
      top: `${y}px`,
    })
  }

  async function show() {
    await update()
    dataShow = true
  }

  function hide() {
    dataShow = false
  }

  onMount(() => {
    update()
    id = Math.random().toString(16).slice(2) // Random id for the tooltip

    // Bind events
    const events: [keyof HTMLElementEventMap, EventListener][] = [
      ['mouseenter', show],
      ['focus', show],
      ['mouseleave', hide],
      ['blur', hide],
    ]
    events.forEach(([event, handler]) => trigger?.addEventListener(event, handler))
    return () => {
      events.forEach(([event, handler]) => trigger?.removeEventListener(event, handler))
    }
  })
</script>

<div bind:this={trigger} aria-describedby={id} class="wrapper">
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
  .wrapper {
    display: inline-block;
  }
  .svelte-hint-tooltip {
    position: absolute;
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
