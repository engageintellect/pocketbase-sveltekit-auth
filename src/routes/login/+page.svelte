<script lang="ts">
  import { enhance } from '$app/forms'
  import Input from '$lib/components/Input.svelte'
  import toast from 'svelte-french-toast'
  export let form
  let loading = false

  const submitLogin = () => {
    loading = true
    return async ({ result, update }: any) => {
      switch (result.type) {
        case 'success':
          await update()
          break
        case 'fail':
          toast.error('Invalid credentials')
          await update()
          break
        case 'error':
          toast.error(result.error.message)
          break
        default:
          await update()
      }
      loading = false
    }
  }
</script>

<form action="?/login" method="POST" class="card" use:enhance={submitLogin}>
  <h1 class="text-2xl mb-8">Login</h1>

  <div class="form-control gap-0 mb-4">
    <Input
      type="email"
      id="email"
      label="Email"
      value={form?.data?.email ?? ''}
      errors={form?.errors?.email}
      placeholder={'Email'}
    />
    <Input
      type="password"
      id="password"
      label="Password"
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
  <!-- {#if form?.notVerified}
			<div class="alert alert-error shadow-lg w-full max-w-lg">
				<div>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="stroke-current flex-shrink-0 h-6 w-6"
						fill="none"
						viewBox="0 0 24 24"
						><path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
						/></svg
					>
					<span>You must verify your email before you can login.</span>
				</div>
			</div>
		{/if} -->
</form>
