<script context="module" lang="ts">
  import { persisted } from 'svelte-local-storage-store'
  import { derived, writable } from 'svelte/store'
  import type { TweaksState } from './Types.svelte'

  const defaultTweaks: TweaksState = {
    compactMessages: false,
    superCompactMessages: false,
    extraWideMessages: false,
    alignUserRight: true,
    bubbleShadowStrong: false,
    bubbleShadowSoft: true,
    roundedMessages: true,
    squareMessages: false,

    highContrastTheme: false,
    neonTheme: false,
    matrixTheme: false,
    sunsetTheme: false,
    desaturatedTheme: false,
    tintedUserMessages: false,
    tintedAssistantMessages: false,
    invertedMessages: false,
    backgroundGrid: false,
    backgroundNoise: false,

    floatMessages: false,
    pulseNewMessages: false,
    slideInMessages: false,
    wobbleMessages: false,
    glowOnHover: false,
    slowTransitions: false,
    ultraFastTransitions: false,
    reduceMotion: false,
    parallaxBackground: false,

    highlightEditableMessages: true,
    showHistoryPreviewInline: false,
    showHistoryCountBadge: false,
    autoOpenHistoryOnEdit: false,
    confirmBeforeRestoreHistory: false,
    lockSystemMessages: false,
    lockErrorMessages: false,
    emphasizeUserEdits: false,
    showEditTimestamp: false,
    showSandboxRibbon: true,

    showTokenUsageInline: true,
    showTokenUsageRight: false,
    showModelBadgePerMessage: false,
    debugLayoutBorders: false,
    debugScrollAnchors: false,
    showMessageUuid: false,
    showMessageRoleBadge: false,
    showMessageIndex: false,
    showRunningTotalsBar: false,

    rainbowUserMessages: false,
    rainbowAssistantMessages: false,
    asciiArtBackground: false,
    snowOverlay: false,
    spotlightActiveMessage: false,
    discoMode: false
  }

  export const tweaksStorage = persisted<TweaksState>('tweaks', defaultTweaks)

  export const tweaksVisible = writable(false)

  let appliedClasses: string[] = []

  export const tweaksApplied = derived(tweaksStorage, ($tweaks) => {
    if (typeof document === 'undefined') return $tweaks

    const root = document.documentElement

    // Remove previously applied tweak classes
    appliedClasses.forEach((cls) => root.classList.remove(cls))

    const nextApplied: string[] = []

    Object.entries($tweaks).forEach(([key, value]) => {
      const cls = `tweak-${key}`
      if (value) {
        root.classList.add(cls)
        nextApplied.push(cls)
      }
    })

    appliedClasses = nextApplied
    return $tweaks
  })
</script>

