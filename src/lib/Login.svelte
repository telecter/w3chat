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
  <section class="mx-md-auto w-md-25">
    <header>
      <h1>W<sup>3</sup> Chat</h1>
      <p class="text-body-secondary">Please login or sign up to continue.</p>
    </header>
    <form on:submit|preventDefault>
      <label for="username" class="form-label">Username</label>
      <input type="text" placeholder="idiot123" id="username" class="form-control" bind:value={username}>
      <label for="password" class="form-label">Password</label>
      <input type="password" placeholder="verysecurepassword25" id="password" class="form-control" bind:value={password}>
      <div class="mt-4 mb-2 pb-3 border-bottom">
        <button on:click={login} class="btn btn-primary">Login</button>
        <button on:click={signup} class="btn btn-secondary">Sign Up</button>
      </div>
      <div class="text-center text-body-secondary">
        Forgot your password? <span class="text-danger">Too bad.</span>
      </div>
    </form>
      <div class="alert alert-danger mt-5" role="alert" style="visibility: {error}">
        <h5 class="alert-heading">Authentication failed.</h5>
        And I have absolutely no idea why.
      </div>
    </section>
{/if}