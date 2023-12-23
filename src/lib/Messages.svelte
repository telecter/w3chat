<script lang="ts">
  import { onDestroy, onMount, tick } from "svelte";
  import client from './pocketbase'

  let messages: any = []
  let unsubscribe: () => void;
  let newMessage: string
  let messagebox: Element;

  onMount(async () => {
    const records = await client.collection('messages').getList(1, 50, { expand: 'user' })
    messages = records.items
    unsubscribe = await client.collection('messages').subscribe('*', async ({ action, record }) => {
      if (action === 'create') {
        const user = await client.collection('users').getOne(record.user)
        record.expand = { user }
        messages = [...messages, record]
        await tick()
        messagebox.lastElementChild?.scrollIntoView()
      }
    })
  })
  onDestroy(() => {
    unsubscribe()
  })
  function send() {
    client.collection('messages').create({
      user: client.authStore.model?.id,
      content: newMessage
    })
    newMessage = ""
  }

</script>

<div class="container">
<div id="messagebox" bind:this={messagebox}>
  {#each messages as message}
    <p><b>{message.expand?.user?.username}: </b>{message.content}</p>
  {/each}
</div>
<form on:submit|preventDefault={send}>
  <input type="text" placeholder="Message" bind:value={newMessage}>
  <button>Send</button>
</form>
</div>


<style>
  #messagebox {
    overflow: auto;
    height: 65vh;
    width: auto;
  }
  .container {
    width: auto;
  }
  input {
    width: 90%;
  }
</style>