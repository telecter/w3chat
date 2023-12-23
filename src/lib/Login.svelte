<script lang="ts">
  import client from './pocketbase'

  let username: string;
  let password: string;
  let error = "hidden"

  function logout() {
    client.authStore.clear()
  }

  async function login() {
    try {
        await client.collection('users').authWithPassword(username, password)
        console.log("Authenticated")
        location.reload()
    }
    catch (err) {
        error = "visible"
    }
  }

  async function signup() {
    try {
        await client.collection('users').create({
            username: username,
            password: password,
            passwordConfirm: password
        })
        await client.collection('users').authWithPassword(username, password)
        console.info("Signed up and authenticated")
        location.reload()
    }
    catch (err) {
        error = "visible"
    }
  }
</script>

{#if client.authStore.isValid}
  <p>Logged in as <b>{client.authStore.model?.username}</b> • <a href="/" on:click={logout}>Logout</a></p>
{:else}
  <div class="container">
    <h1>W<sup>3</sup> Chat</h1>
    <p>Please login or sign up to continue.</p>
    <form on:submit|preventDefault>
        <input type="text" placeholder="Username" bind:value={username}>
        <input type="password" placeholder="Password" bind:value={password}>
        <p id="error-text" style="visibility:{error}">Authentication failed.</p>
        <button on:click={login}>Login</button>
        <button on:click={signup}>Sign Up</button>
      </form>
  </div>
{/if}


<style>
    input {
      display: block;
      width: 90%;
    }
    .container {
        margin: auto;
        width: 30%;
    }
    @media only screen and (max-width: 600px) {
        .container {
            width: 90%;
        }
    }
    #error-text {
        color: rgb(228, 67, 67);
    }
</style>