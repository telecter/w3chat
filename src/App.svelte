<script lang="ts">
  import Login from './lib/Login.svelte'
  import Messages from './lib/Messages.svelte';
  import client from './lib/pocketbase'
</script>

<main class="container mt-5">
  {#if client.authStore.isValid}
    <Messages />
  {:else}
    <Login />
  {/if}
</main>
