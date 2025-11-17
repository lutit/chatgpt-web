<script context="module" lang="ts">
  import { writable } from 'svelte/store'
  export const miniOSVisible = writable(false)
</script>

<script lang="ts">
  import { onDestroy, onMount } from 'svelte'
  import { miniOSVisible } from './MiniOS.svelte'
  import { tweaksVisible } from './Tweaks.svelte'
  import { openChatWindow } from './ChatWindows.svelte'
  import { lastChatId, chatsStorage } from './Storage.svelte'
  import { get } from 'svelte/store'

  let now: Date = new Date()
  let clockTimer: number | undefined

  const openAnyChat = () => {
    let chatId = get(lastChatId) || 0
    const chats = get(chatsStorage)
    if (!chatId && chats && chats[0]) {
      chatId = chats[0].id
    }
    if (chatId) {
      openChatWindow(chatId)
    }
  }

  const openTweaks = () => {
    tweaksVisible.set(true)
  }

  const exitMiniOS = () => {
    miniOSVisible.set(false)
  }

  onMount(() => {
    clockTimer = setInterval(() => {
      now = new Date()
    }, 60000) as unknown as number
  })

  onDestroy(() => {
    if (clockTimer) clearInterval(clockTimer)
  })

  const formatTime = (date: Date) => {
    return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
  }
</script>

{#if $miniOSVisible}
  <div class="mini-os-overlay" on:click={() => miniOSVisible.set(false)}>
    <div class="mini-os-desktop" on:click|stopPropagation>
      <header class="mini-os-topbar">
        <div class="mini-os-logo">MiniOS</div>
        <div class="mini-os-topbar-center">
          <span class="pill">Chat Desktop</span>
        </div>
        <div class="mini-os-topbar-right">
          <span class="status-dot"></span>
          <span>{formatTime(now)}</span>
        </div>
      </header>

      <main class="mini-os-main">
        <div class="mini-os-widgets">
          <section class="widget">
            <h3>Quick Start</h3>
            <button class="widget-button" on:click={openAnyChat}>Open Chat Window</button>
            <button class="widget-button" on:click={openTweaks}>Open Tweaks Lab</button>
          </section>
          <section class="widget">
            <h3>About</h3>
            <p>This is a miniature desktop around your chat windows. Drag, stack and play like a tiny OS inside the app.</p>
          </section>
        </div>
      </main>

      <footer class="mini-os-dock">
        <div class="mini-os-dock-inner">
          <button class="dock-icon" on:click={openAnyChat} title="Chat">
            <span>💬</span>
          </button>
          <button class="dock-icon" on:click={openTweaks} title="Tweaks">
            <span>🧪</span>
          </button>
          <button class="dock-icon" on:click={exitMiniOS} title="Exit MiniOS">
            <span>⏻</span>
          </button>
        </div>
      </footer>
    </div>
  </div>
{/if}

