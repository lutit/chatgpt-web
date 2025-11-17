<script lang="ts">
  import Fa from 'svelte-fa/src/fa.svelte'
  import {
    faXmark,
    faFlask,
    faSliders,
    faBolt,
    faWandMagicSparkles,
    faGaugeHigh,
    faBug
  } from '@fortawesome/free-solid-svg-icons/index'
  import { tweaksStorage, tweaksVisible } from './Tweaks.svelte'
  import type { TweaksState } from './Types.svelte'

  type TweakKey = keyof TweaksState

  type TweakConfig = {
    key: TweakKey;
    label: string;
    description: string;
  }

  type TweakGroup = {
    id: string;
    title: string;
    icon?: any;
    description: string;
    items: TweakConfig[];
  }

  const groups: TweakGroup[] = [
    {
      id: 'layout',
      title: 'Layout & Density',
      icon: faSliders,
      description: 'Control how tight, wide or structured the chat stream feels.',
      items: [
        { key: 'compactMessages', label: 'Compact bubbles', description: 'Reduce vertical padding on every message.' },
        { key: 'superCompactMessages', label: 'Ultra compact', description: 'Shrink messages aggressively for dense logs.' },
        { key: 'extraWideMessages', label: 'Extra wide column', description: 'Stretch chat column a bit wider than default.' },
        { key: 'alignUserRight', label: 'User on right edge', description: 'Pin user messages harder to the right.' },
        { key: 'bubbleShadowStrong', label: 'Strong bubble shadow', description: 'Boost message card elevation and depth.' },
        { key: 'bubbleShadowSoft', label: 'Soft bubble shadow', description: 'Use subtle, low‑contrast shadows instead.' },
        { key: 'roundedMessages', label: 'Rounded bubbles', description: 'Make all messages fully pill‑shaped.' },
        { key: 'squareMessages', label: 'Square bubbles', description: 'Remove rounding for a terminal‑like look.' },
        { key: 'backgroundGrid', label: 'Subtle grid background', description: 'Add a faint grid under the conversation.' },
        { key: 'backgroundNoise', label: 'Paper noise texture', description: 'Overlay a subtle noise pattern on the app.' }
      ]
    },
    {
      id: 'theme',
      title: 'Themes & Color Experiments',
      icon: faWandMagicSparkles,
      description: 'Flip the chat into wild, high‑contrast or neon themes.',
      items: [
        { key: 'highContrastTheme', label: 'High contrast', description: 'Sharpen text and bubbles for low‑light readability.' },
        { key: 'neonTheme', label: 'Neon edges', description: 'Give bubbles a neon‑style glow and accents.' },
        { key: 'matrixTheme', label: 'Matrix night', description: 'Deep greens on charcoal for hacker vibes.' },
        { key: 'sunsetTheme', label: 'Sunset gradient', description: 'Warm purple‑orange gradients behind chat.' },
        { key: 'desaturatedTheme', label: 'Desaturate colors', description: 'Mute all colors for a calm, editorial feel.' },
        { key: 'tintedUserMessages', label: 'Tint user messages', description: 'Add a hint of color only to your messages.' },
        { key: 'tintedAssistantMessages', label: 'Tint assistant messages', description: 'Give assistant replies a soft highlight.' },
        { key: 'invertedMessages', label: 'Invert bubbles', description: 'Swap light/dark contrast inside message cards.' },
        { key: 'asciiArtBackground', label: 'ASCII backdrop', description: 'Overlay faint ASCII art texture behind content.' },
        { key: 'discoMode', label: 'Disco gradients', description: 'Cycle subtle background gradients over time.' }
      ]
    },
    {
      id: 'motion',
      title: 'Motion & Micro‑Interactions',
      icon: faBolt,
      description: 'Animations for messages, typing and scrolling behaviour.',
      items: [
        { key: 'floatMessages', label: 'Floating messages', description: 'Give bubbles a tiny vertical float animation.' },
        { key: 'pulseNewMessages', label: 'Pulse on new replies', description: 'Briefly pulse the newest assistant message.' },
        { key: 'slideInMessages', label: 'Slide‑in history', description: 'Slide messages in as they appear.' },
        { key: 'wobbleMessages', label: 'Wobble on hover', description: 'Very slight wobble when hovering over messages.' },
        { key: 'glowOnHover', label: 'Glow on hover', description: 'Highlight the active bubble under your cursor.' },
        { key: 'slowTransitions', label: 'Slow transitions', description: 'Stretch layout transitions for a cinematic feel.' },
        { key: 'ultraFastTransitions', label: 'Ultra‑fast transitions', description: 'Snap everything instantly, no easing.' },
        { key: 'reduceMotion', label: 'Reduce motion', description: 'Tone down non‑essential animations overall.' },
        { key: 'parallaxBackground', label: 'Parallax background', description: 'Add a subtle parallax drift to the backdrop.' }
      ]
    },
    {
      id: 'sandbox',
      title: 'Sandbox & Editing',
      icon: faFlask,
      description: 'Tweaks that change how the sandbox editing experience behaves.',
      items: [
        { key: 'highlightEditableMessages', label: 'Highlight editable messages', description: 'Softly outline messages when sandbox mode is active.' },
        { key: 'showHistoryPreviewInline', label: 'Inline history preview', description: 'Show more of each historic version inside History.' },
        { key: 'showHistoryCountBadge', label: 'Show history count badge', description: 'Display how many times a message was edited.' },
        { key: 'autoOpenHistoryOnEdit', label: 'Auto‑open history after edits', description: 'Expand the history panel after you finish editing.' },
        { key: 'confirmBeforeRestoreHistory', label: 'Confirm restore', description: 'Ask for confirmation before restoring an old version.' },
        { key: 'lockSystemMessages', label: 'Lock system messages', description: 'Prevent editing of system‑role messages in sandbox.' },
        { key: 'lockErrorMessages', label: 'Lock error messages', description: 'Prevent editing responses that represent errors.' },
        { key: 'emphasizeUserEdits', label: 'Emphasize edited messages', description: 'Subtly mark messages that have been changed.' },
        { key: 'showEditTimestamp', label: 'Show last edit time', description: 'Show when a message was last edited in sandbox.' },
        { key: 'showSandboxRibbon', label: 'Sandbox ribbon', description: 'Add a persistent ribbon when sandbox mode is on.' }
      ]
    },
    {
      id: 'nerd',
      title: 'Nerd Stats & Debug',
      icon: faGaugeHigh,
      description: 'Expose extra structure, indices and token nerd‑stats.',
      items: [
        { key: 'showTokenUsageInline', label: 'Prominent token usage', description: 'Emphasize token usage text beneath messages.' },
        { key: 'showTokenUsageRight', label: 'Right‑aligned token usage', description: 'Align token usage to the right side of the bubble.' },
        { key: 'showModelBadgePerMessage', label: 'Model badges', description: 'Show which model generated each reply.' },
        { key: 'debugLayoutBorders', label: 'Debug layout borders', description: 'Outline major layout regions for inspection.' },
        { key: 'debugScrollAnchors', label: 'Show scroll anchors', description: 'Show markers where scroll helpers target.' },
        { key: 'showMessageUuid', label: 'Show message UUID', description: 'Surface internal UUIDs for every message.' },
        { key: 'showMessageRoleBadge', label: 'Role badges', description: 'Display the role (user/assistant/system) on each bubble.' },
        { key: 'showMessageIndex', label: 'Message index', description: 'Number each message down the conversation.' },
        { key: 'showRunningTotalsBar', label: 'Running totals bar', description: 'Highlight overall token/cost totals at the bottom.' }
      ]
    },
    {
      id: 'fun',
      title: 'Fun & Experimental',
      icon: faBug,
      description: 'Purely playful tweaks that bend the chat a bit.',
      items: [
        { key: 'rainbowUserMessages', label: 'Rainbow user bubbles', description: 'Cycle through hues on your own messages.' },
        { key: 'rainbowAssistantMessages', label: 'Rainbow assistant bubbles', description: 'Cycle assistant replies through a color band.' },
        { key: 'snowOverlay', label: 'Snow overlay', description: 'Add a soft falling snow particle overlay.' },
        { key: 'spotlightActiveMessage', label: 'Spotlight active message', description: 'Dim others when you hover a single bubble.' },
        { key: 'asciiArtBackground', label: 'ASCII noise overlay', description: 'Overlay faint ASCII patterns behind content.' },
        { key: 'discoMode', label: 'Disco stripe mode', description: 'Give the app a constantly shifting stripe glow.' }
      ]
    }
  ]

  let tweaks: TweaksState
  $: tweaks = $tweaksStorage

  const toggle = (key: TweakKey) => {
    tweaksStorage.update((state) => ({
      ...state,
      [key]: !state[key]
    }))
  }
</script>

{#if $tweaksVisible}
  <div class="tweaks-panel-backdrop" on:click={() => tweaksVisible.set(false)} />
  <aside class="tweaks-panel" on:click|stopPropagation>
    <header class="tweaks-panel-header">
      <div class="tweaks-panel-title">
        <span class="icon primary"><Fa icon={faFlask} /></span>
        <div>
          <h2>Chat Tweaks Lab</h2>
          <p>Live‑tune layout, themes, sandbox behaviour and fun experiments.</p>
        </div>
      </div>
      <button class="button is-small close-button" on:click={() => tweaksVisible.set(false)} aria-label="Close tweaks lab">
        <span class="icon"><Fa icon={faXmark} /></span>
      </button>
    </header>

    <section class="tweaks-panel-body">
      {#each groups as group}
        <section class="tweak-group" id={group.id}>
          <div class="tweak-group-header">
            {#if group.icon}
              <span class="icon"><Fa icon={group.icon} /></span>
            {/if}
            <div>
              <h3>{group.title}</h3>
              <p>{group.description}</p>
            </div>
          </div>
          <div class="tweak-list">
            {#each group.items as item}
              <label class="tweak-item">
                <div class="tweak-item-main">
                  <input
                    type="checkbox"
                    checked={!!tweaks[item.key]}
                    on:change={() => toggle(item.key)}
                  />
                  <span class="tweak-label">{item.label}</span>
                </div>
                <div class="tweak-description">{item.description}</div>
              </label>
            {/each}
          </div>
        </section>
      {/each}
    </section>
  </aside>
{/if}

