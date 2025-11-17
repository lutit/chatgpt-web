<script lang="ts">
  import { chatWindows, closeChatWindow, focusChatWindow, moveChatWindow, toggleMinimizeWindow, toggleMaximizeWindow, windowStyle } from './ChatWindows.svelte'
  import Chat from './Chat.svelte'
  import type { ChatWindow, ChatWindowStyle } from './ChatWindows.svelte'

  let draggingId: string | null = null
  let lastX = 0
  let lastY = 0

  const onHeaderPointerDown = (event: PointerEvent, win: ChatWindow) => {
    event.stopPropagation()
    draggingId = win.id
    lastX = event.clientX
    lastY = event.clientY
    focusChatWindow(win.id)
    window.addEventListener('pointermove', onPointerMove)
    window.addEventListener('pointerup', onPointerUp)
  }

  const onPointerMove = (event: PointerEvent) => {
    if (!draggingId) return
    const dx = event.clientX - lastX
    const dy = event.clientY - lastY
    lastX = event.clientX
    lastY = event.clientY
    moveChatWindow(draggingId, dx, dy)
  }

  const onPointerUp = () => {
    draggingId = null
    window.removeEventListener('pointermove', onPointerMove)
    window.removeEventListener('pointerup', onPointerUp)
  }

  const getWindowClass = (style: ChatWindowStyle) => {
    if (style === 'macos') return 'style-macos'
    if (style === 'qt') return 'style-qt'
    return 'style-windows'
  }
</script>

{#if $chatWindows.length}
  <div class="chat-window-layer">
    {#each $chatWindows as win (win.id)}
      <div
        class={`chat-window ${getWindowClass($windowStyle)} ${win.minimized ? 'is-minimized' : ''}`}
        style={`left:${win.x}px;top:${win.y}px;width:${win.width}px;height:${win.height}px;z-index:${win.zIndex};`}
        on:mousedown={() => focusChatWindow(win.id)}
      >
        <div
          class="chat-window-header"
          on:pointerdown={(e) => onHeaderPointerDown(e, win)}
        >
          <div class="window-controls">
            <button class="close" on:click|stopPropagation={() => closeChatWindow(win.id)} />
            <button class="minimize" on:click|stopPropagation={() => toggleMinimizeWindow(win.id)} />
            <button class="maximize" on:click|stopPropagation={() => toggleMaximizeWindow(win.id)} />
          </div>
          <div class="window-title">
            {win.title}
          </div>
        </div>
        {#if !win.minimized}
          <div class="chat-window-body">
            <Chat params={{ chatId: String(win.chatId) }} />
          </div>
        {/if}
      </div>
    {/each}
  </div>
{/if}

