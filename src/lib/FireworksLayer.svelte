<script lang="ts">
  import { fireworksBursts } from './Fireworks.svelte'
</script>

{#if $fireworksBursts.length}
  <div class="fireworks-layer">
    {#each $fireworksBursts as burst (burst.id)}
      <div
        class="firework-burst"
        style={`left:${burst.x}%;top:${burst.y}%;--fw-hue:${burst.hue};`}
      />
    {/each}
  </div>
{/if}

