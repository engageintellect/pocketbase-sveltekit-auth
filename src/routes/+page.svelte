<script lang="ts">
  import { currentUser } from '$lib/stores/user'
  import { fade } from 'svelte/transition'
</script>

<div>
  <div class="py-5" in:fade={{ delay: 0, duration: 500 }}>
    {#if $currentUser}
      <h1 class="text-primary font-bold">Secret content</h1>
      <p>Only logged in users are able to see this message.</p>

      <a href="/my/settings/profile">profile</a>
    {:else}
      <h1>Welcome to SvelteKit</h1>
      <p>
        Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation
      </p>
    {/if}
  </div>
</div>
