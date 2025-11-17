<script context="module" lang="ts">
  import { writable, get } from 'svelte/store'
  import { v4 as uuidv4 } from 'uuid'
  import { tweaksStorage } from './Tweaks.svelte'
  import type { TweaksState } from './Types.svelte'

  export type FireworkBurst = {
    id: string;
    x: number;
    y: number;
    hue: number;
  };

  const BURST_LIFETIME = 1200

  export const fireworksBursts = writable<FireworkBurst[]>([])

  export const triggerFireworks = () => {
    const tweaks = get(tweaksStorage) as TweaksState
    if (!tweaks.fireworksOnSend) return

    const bursts: FireworkBurst[] = Array.from({ length: 3 }).map(() => ({
      id: uuidv4(),
      x: 15 + Math.random() * 70,
      y: 10 + Math.random() * 40,
      hue: 200 + Math.random() * 120
    }))

    fireworksBursts.update((all) => [...all, ...bursts])

    setTimeout(() => {
      fireworksBursts.update((all) =>
        all.filter((b) => !bursts.find((x) => x.id === b.id))
      )
    }, BURST_LIFETIME)
  }
</script>

