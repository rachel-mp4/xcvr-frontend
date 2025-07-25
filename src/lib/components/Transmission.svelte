<script lang="ts">
    import type { Message } from "$lib/types";
    import { computePosition, flip, shift, offset } from "@floating-ui/dom";
    import { hexToContrast, hexToTransparent, numToHex } from "$lib/colors";
    import ProfileCard from "./ProfileCard.svelte";
    interface Props {
        message: Message;
        margin: number;
    }
    let { message, margin }: Props = $props();
    let color: string = numToHex(message.color ?? 16777215);
    let contrast: string = hexToContrast(color);
    let partial: string = hexToTransparent(contrast);
    let triggerEl: HTMLElement | undefined = $state();
    let profileEl: HTMLElement | undefined = $state();
    let showProfile = $state(false);
    async function updatePosition() {
        if (triggerEl && profileEl) {
            const { x, y } = await computePosition(triggerEl, profileEl, {
                middleware: [offset(0), flip(), shift()],
            });
            Object.assign(profileEl.style, {
                left: `${x}px`,
                top: `${y}px`,
            });
        }
    }
    $effect(() => {
        if (showProfile) {
            updatePosition();
        }
    });
</script>

<div
    style:--theme={color}
    style:--tcontrast={contrast}
    style:--tpartial={partial}
    style:--margin={margin + "rem"}
    class="{message.active ? 'active' : ''} 
    {message.profileView ? 'signed' : ''} 
    transmission"
>
    <div class="header">
        {message.nick}{#if message.handle}
            {#if !message.profileView}
                <span class="handle">
                    @{message.handle}
                </span>
            {:else}
                <div
                    role="button"
                    tabindex="0"
                    class="handle-container"
                    onmouseenter={() => (showProfile = true)}
                    onmouseleave={() => (showProfile = false)}
                >
                    <a
                        bind:this={triggerEl}
                        class="handle"
                        href={`/p/${message.handle}`}
                        >@{message.handle}
                    </a>
                    {#if showProfile}
                        <div class="profile-container" bind:this={profileEl}>
                            <ProfileCard profile={message.profileView} />
                        </div>
                    {/if}
                </div>
            {/if}
        {/if}
    </div>
    <div class="body">{message.body}</div>
</div>

<style>
    .active {
        background-color: var(--theme);
        color: var(--tcontrast);
    }
    .header {
        font-weight: 700;
    }
    .active .handle {
        color: var(--tpartial);
    }
    .active.signed .handle {
        color: var(--tcontrast);
    }
    .signed .handle {
        color: var(--fg);
    }
    .handle {
        color: var(--fl);
        position: relative;
    }

    .handle::after {
        content: "";
        color: var(--fg);
        background: var(--bg);
        position: absolute;
        left: 0;
        right: 0;
        top: calc(55% - 0.125rem);
        bottom: calc(45% - 0.125rem);
        transform: scaleX(0);
        transform-origin: center left;
        transition: transform 0.17s 3s;
    }

    .transmission:not(.signed):not(.active) .handle::after {
        transform: scaleX(1);
    }
    .transmission:not(.signed):not(.active) .handle:hover::after {
        content: "i couldn't find a record :c";
    }

    .transmission {
        padding-bottom: 1rem;
        margin-top: var(--margin);
    }

    .transmission:not(.active) .header {
        color: var(--theme);
        text-shadow: 0 0 1rem var(--fl);
    }
    .body {
        white-space: pre-wrap;
        word-wrap: break-word;
        max-width: 100%;
    }
    .profile-container {
        position: absolute;
        z-index: 1;
    }
    .handle-container {
        display: inline-block;
        color: var(--fg);
    }
</style>
