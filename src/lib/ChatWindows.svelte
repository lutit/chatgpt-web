<script context="module" lang="ts">
  import { writable, get } from 'svelte/store'
  import { persisted } from 'svelte-local-storage-store'
  import { getChat } from './Storage.svelte'
  import type { Chat } from './Types.svelte'
  import { v4 as uuidv4 } from 'uuid'

  export type ChatWindowStyle = 'qt' | 'macos' | 'windows'

  export type ChatWindow = {
    id: string;
    chatId: number;
    title: string;
    x: number;
    y: number;
    width: number;
    height: number;
    minimized: boolean;
    maximized: boolean;
    zIndex: number;
  };

  export const chatWindows = writable<ChatWindow[]>([])

  export const windowStyle = persisted<ChatWindowStyle>('chatWindowStyle', 'windows')

  let topZ = 100

  const createWindowForChat = (chat: Chat): ChatWindow => {
    const baseWidth = 720
    const baseHeight = 520
    const offset = (get(chatWindows).length % 5) * 26

    return {
      id: uuidv4(),
      chatId: chat.id,
      title: chat.name || `Chat ${chat.id}`,
      x: 80 + offset,
      y: 60 + offset,
      width: baseWidth,
      height: baseHeight,
      minimized: false,
      maximized: false,
      zIndex: ++topZ
    }
  }

  export const openChatWindow = (chatId: number) => {
    const chat = getChat(chatId)
    if (!chat) return
    chatWindows.update((wins) => {
      const existing = wins.find((w) => w.chatId === chatId && !w.minimized)
      if (existing) {
        existing.zIndex = ++topZ
        return [...wins]
      }
      const win = createWindowForChat(chat)
      return [...wins, win]
    })
  }

  export const closeChatWindow = (id: string) => {
    chatWindows.update((wins) => wins.filter((w) => w.id !== id))
  }

  export const focusChatWindow = (id: string) => {
    chatWindows.update((wins) => {
      return wins.map((w) => {
        if (w.id === id) {
          return { ...w, zIndex: ++topZ }
        }
        return w
      })
    })
  }

  export const moveChatWindow = (id: string, dx: number, dy: number) => {
    if (typeof window === 'undefined') return
    const maxX = window.innerWidth
    const maxY = window.innerHeight
    chatWindows.update((wins) => wins.map((w) => {
      if (w.id !== id || w.maximized) return w
      const nextX = Math.min(Math.max(w.x + dx, 0), Math.max(maxX - 260, 0))
      const nextY = Math.min(Math.max(w.y + dy, 0), Math.max(maxY - 120, 0))
      return { ...w, x: nextX, y: nextY }
    }))
  }

  export const toggleMinimizeWindow = (id: string) => {
    chatWindows.update((wins) => wins.map((w) => {
      if (w.id !== id) return w
      return { ...w, minimized: !w.minimized }
    }))
  }

  export const toggleMaximizeWindow = (id: string) => {
    if (typeof window === 'undefined') return
    chatWindows.update((wins) => wins.map((w) => {
      if (w.id !== id) return w
      if (!w.maximized) {
        return {
          ...w,
          x: 40,
          y: 40,
          width: Math.max(window.innerWidth - 100, 320),
          height: Math.max(window.innerHeight - 120, 260),
          maximized: true,
          minimized: false
        }
      } else {
        return { ...w, maximized: false }
      }
    }))
  }
</script>

