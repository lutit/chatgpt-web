<script context="module" lang="ts">
  import { persisted } from 'svelte-local-storage-store'
  import { derived, writable } from 'svelte/store'
  import type { TweaksState, Message } from './Types.svelte'

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
    discoMode: false,
    blackoutOverlay: false,
    glitchAssistantText: false,
    tiltMessages: false,
    zebraChat: false,
    breathingInput: false,
    rainbowScrollbars: false,
    haloUserMessages: false,
    haloAssistantMessages: false,
    floatingSidebar: false,
    glassChat: false,
    gradientHeader: false,
    archInstallEmulator: false
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

  type EffectId =
    | 'blackout'
    | 'disco'
    | 'matrix'
    | 'rainbowUser'
    | 'rainbowAssistant'
    | 'spotlight'
    | 'snow'
    | 'tinyChat'
    | 'bigChat'
    | 'calm'

  const effectDefaults: Record<EffectId, number> = {
    blackout: 30000,
    disco: 30000,
    matrix: 25000,
    rainbowUser: 25000,
    rainbowAssistant: 25000,
    spotlight: 20000,
    snow: 25000,
    tinyChat: 30000,
    bigChat: 30000,
    calm: 1
  }

  const MAX_EFFECT_DURATION_MS = 60000

  const clampDuration = (ms:number|undefined, id:EffectId):number => {
    const base = effectDefaults[id] || 0
    if (!ms || Number.isNaN(ms)) return base
    return Math.min(Math.max(ms, 1000), MAX_EFFECT_DURATION_MS)
  }

  const applyTweaks = (patch: Partial<TweaksState>) => {
    tweaksStorage.update((state) => ({
      ...state,
      ...patch
    }))
  }

  const scheduleReset = (keys: (keyof TweaksState)[], durationMs:number) => {
    if (typeof window === 'undefined' || !durationMs) return
    window.setTimeout(() => {
      tweaksStorage.update((state) => {
        const next = { ...state }
        keys.forEach((key) => {
          next[key] = false as any
        })
        return next
      })
    }, durationMs)
  }

  export const triggerEffect = (id:EffectId, durationMs?:number) => {
    const ms = clampDuration(durationMs, id)
    switch (id) {
      case 'blackout':
        applyTweaks({ blackoutOverlay: true })
        scheduleReset(['blackoutOverlay'], ms)
        break
      case 'disco':
        applyTweaks({ discoMode: true })
        scheduleReset(['discoMode'], ms)
        break
      case 'matrix':
        applyTweaks({ matrixTheme: true })
        scheduleReset(['matrixTheme'], ms)
        break
      case 'rainbowUser':
        applyTweaks({ rainbowUserMessages: true })
        scheduleReset(['rainbowUserMessages'], ms)
        break
      case 'rainbowAssistant':
        applyTweaks({ rainbowAssistantMessages: true })
        scheduleReset(['rainbowAssistantMessages'], ms)
        break
      case 'spotlight':
        applyTweaks({ spotlightActiveMessage: true })
        scheduleReset(['spotlightActiveMessage'], ms)
        break
      case 'snow':
        applyTweaks({ snowOverlay: true })
        scheduleReset(['snowOverlay'], ms)
        break
      case 'tinyChat':
        applyTweaks({ superCompactMessages: true })
        scheduleReset(['superCompactMessages'], ms)
        break
      case 'bigChat':
        applyTweaks({ extraWideMessages: true })
        scheduleReset(['extraWideMessages'], ms)
        break
      case 'calm':
        applyTweaks({
          discoMode: false,
          matrixTheme: false,
          rainbowUserMessages: false,
          rainbowAssistantMessages: false,
          spotlightActiveMessage: false,
          snowOverlay: false,
          blackoutOverlay: false
        })
        break
    }
  }

  type ParsedEffect = {
    id: EffectId;
    durationMs?: number;
  }

  const parsePrankEffects = (content:string): ParsedEffect[] => {
    const results: ParsedEffect[] = []
    if (!content) return results
    const regex = /\[\[PRANK:([a-zA-Z0-9_-]+)(?::(\d+))?]]/g
    let match
    while ((match = regex.exec(content)) !== null) {
      const rawId = (match[1] || '').trim().toLowerCase()
      const sec = match[2] ? parseInt(match[2], 10) : undefined
      let id: EffectId | undefined
      switch (rawId) {
        case 'blackout': id = 'blackout'; break
        case 'disco': id = 'disco'; break
        case 'matrix': id = 'matrix'; break
        case 'rainbowuser': id = 'rainbowUser'; break
        case 'rainbowassistant': id = 'rainbowAssistant'; break
        case 'spotlight': id = 'spotlight'; break
        case 'snow': id = 'snow'; break
        case 'tinychat': id = 'tinyChat'; break
        case 'bigchat': id = 'bigChat'; break
        case 'calm': id = 'calm'; break
        default: id = undefined
      }
      if (!id) continue
      const ms = sec ? sec * 1000 : undefined
      results.push({ id, durationMs: ms })
    }
    return results
  }

  export const handlePrankEffectsForMessage = (profileKey:string|undefined, message:Message|undefined) => {
    if (!message || message.role !== 'assistant') return
    if (profileKey !== 'visualPrankster') return
    const effects = parsePrankEffects(message.content || '')
    effects.forEach((e) => triggerEffect(e.id, e.durationMs))
  }
</script>
