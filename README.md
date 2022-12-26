# svelte-hint

Svelte library for tooltips internally powered by the awesome [Fluent UI](https://floating-ui.com/) with sensible default values.

Check out the **[Demo](https://svelte-hint.pages.dev/)** to see it in action.

![Screenshot](.github/screen.png)

![version badge](https://badgen.net/npm/v/svelte-hint)
![downloads badge](https://badgen.net/npm/dt/svelte-hint)
![dependency count](https://badgen.net/bundlephobia/dependency-count/svelte-hint)
![minzip size badge](https://badgen.net/bundlephobia/minzip/svelte-hint)
![types badge](https://badgen.net/npm/types/svelte-hint)

## 🌈 Features

- Automatic positioning & overflow handling on screen edges (Fluent UI).
- Fully typed.
- Sensible default values.
- Use text or custom html/components as tooltips.

## 📀 Installation

```bash
npm install svelte-hint
```

## ⌨️ Usage

### With text

```svelte
<script lang="ts">
  import Hint from 'svelte-hint'
</script>

<Hint text="A tooltip!">
  <button class="btn btn-success drag">Hover me!</button>
</Hint>
```

### With custom html / components

```svelte
<script lang="ts">
  import Hint from 'svelte-hint'
</script>

<Hint>
  <button class="btn btn-success drag">Hover me!</button>
  <i slot="hint">Some custom html</i>
</Hint>
```

## 🗂 Docs

### Props

| Prop        | Type                          | Default               | Description                                                                                                           |
| ----------- | ----------------------------- | --------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `text`      | `string`                      | `''`                  | Text to be used as the tooltip. If empty the slot will be used.                                                       |
| `placement` | `Placement`                   | `auto`                | See the [Fluent UI docs](https://floating-ui.com/docs/computePosition#placement).                                     |
| `boundary`  | `HTMLElement \| string`       | `'clippingAncestors'` | See the [Fluent UI docs](https://floating-ui.com/docs/detectOverflow#boundary).                                       |
| `offset`    | `Options`                     | `4`                   | See the [Fluent UI docs](https://floating-ui.com/docs/offset#options).                                                |
| `auto`      | `boolean \| 'start' \| 'end'` | `false`               | Use the [`autoPlacement`](https://floating-ui.com/docs/autoPlacement) middleware. If set `placement` will be ignored. |

### Slots

#### `hint`

> Only works if the `text` props is empty. Otherwise the slot is ignored.

If you don't want to use the pre-styled tooltip you are free to use whatever html / svelte code you'd like as the tooltip.

```svelte
<Hint>
  <div>Hover me</div>
  <div slot="hint">Some custom html</div>
</Hint>
```
