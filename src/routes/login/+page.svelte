<script lang="ts">
  import { enhance } from '$app/forms'
  import Input from '$lib/components/Input.svelte'
  export let form
  let loading = false
  import { fade } from 'svelte/transition'
</script>

<form action="?/login" method="POST" class="card sm:max-w-md sm:mt-10 mx-auto">
  <div class="mb-5">
    <h1 class="text-7xl">login</h1>
    <p class="pt-2">
      Or <a href="/register" class="underline text-primary">register</a> to create
      an account.
    </p>
  </div>

  <div class="form-control gap-0 mb-4">
    <Input
      type="email"
      id="email"
      value={form?.data?.email ?? ''}
      errors={form?.errors?.email}
      placeholder={'Email'}
    />
    <Input
      type="password"
      id="password"
      value={form?.data?.password ?? ''}
      errors={form?.errors?.password}
      disabled={loading}
      placeholder={'Password'}
    />
    <div class="w-full max-w-lg">
      <a
        href="/reset-password"
        class="font-medium text-primary hover:cursor-pointer hover:underline"
      >
        Forgot Password?</a
      >
    </div>

    <button disabled={loading} class="btn btn-primary">Login</button>
  </div>
</form>
