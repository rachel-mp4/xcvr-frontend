<script lang="ts">
    import { onMount } from "svelte";

    interface Props {
        onBeforeInput?: (event: InputEvent) => void;
        onInput?: (event: Event) => void;
        placeholder?: string;
        value?: string;
        maxlength?: number;
        bold?: boolean;
        color?: string;
    }

    let {
        onBeforeInput,
        placeholder,
        value = $bindable(""),
        onInput,
        maxlength,
        bold = false,
        color,
    }: Props = $props();

    let inputEl: HTMLElement;
    function adjust(event: Event) {
        onInput?.(event);
    }

    function adjustHeight() {
        if (inputEl) {
            inputEl.style.height = "auto";
            inputEl.style.height = inputEl.scrollHeight + "px";
        }
    }
    onMount(adjustHeight);
    $effect(() => {
        value;
        adjustHeight();
    });
</script>

<textarea
    rows="1"
    bind:value
    bind:this={inputEl}
    {maxlength}
    oninput={adjust}
    onbeforeinput={onBeforeInput}
    style:font-weight={bold ? "700" : "inherit"}
    style:--theme={color}
    {placeholder}
></textarea>

<style>
    textarea {
        width: 100%;
        font: inherit;
        padding: 0;
        margin: 0;
        display: block;
        resize: none;
        border: none;
    }
</style>

