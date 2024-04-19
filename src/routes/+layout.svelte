<script lang="ts">
  import '../app.pcss'
  import { applyAction, enhance } from '$app/forms'
  import { pb } from '$lib/pocketbase'
  import { currentUser } from '$lib/stores/user'
  import type { PageData } from './$types'
  import daisyuiColors from 'daisyui/src/theming/themes'
  import { selectedTheme } from '$lib/stores/theme'
  import { onMount } from 'svelte'
  import Icon from '@iconify/svelte'
  import { getImageURL } from '$lib/utils'

  export let data: PageData

  let themes = Object.keys(daisyuiColors)

  // Set the current user from the data passed in from the server
  $: currentUser.set(data.user)

  onMount(() => {
    const storedTheme = localStorage.getItem('selectedTheme')
    if (storedTheme && themes.includes(storedTheme)) {
      selectedTheme.set(storedTheme)
      document.documentElement.setAttribute('data-theme', storedTheme)
    }
  })

  function handleThemeChange(event: any) {
    const theme = event.target.value
    selectedTheme.set(theme)
    localStorage.setItem('selectedTheme', theme)
  }
</script>

<div class="bg-base-100 text-neutral-content">
  <div class="navbar max-w-xl mx-auto text-base-content">
    <div class="flex-1">
      <a href="/" class="btn btn-primary text-xl">PB + SK</a>
    </div>
    <div class="flex-none gap-2">
      <!-- <div class="form-control">
        <input
          type="text"
          placeholder="Search"
          class="input input-bordered w-24 md:w-auto"
        />
      </div> -->

      {#if $currentUser}
        <div class="">
          <div class="dropdown dropdown-end">
            <div tabindex="0" role="button">
              <div class="lg:tooltip lg:tooltip-left" data-tip="Theme Selector">
                <button class="btn btn-ghost">
                  <div class="font-normal lowercase">
                    <Icon icon="gridicons-themes" class="h-6 w-6" />
                  </div>
                </button>
              </div>
            </div>
            <ul
              tabindex="-1"
              class="dropdown-content dropdown-end rounded-box border-primary bg-base-100 -z-[-1] mt-3 h-96 w-52 overflow-auto border p-2 shadow"
            >
              {#each themes.sort() as theme}
                <li>
                  <input
                    type="radio"
                    name="theme-dropdown"
                    class="theme-controller text-base-content btn btn-ghost btn-sm btn-block justify-start font-medium"
                    aria-label={theme}
                    value={theme}
                    on:change={handleThemeChange}
                  />
                </li>
              {/each}
            </ul>
          </div>
        </div>

        <div class="dropdown dropdown-end text-base-content -z-[-1]">
          <div
            tabindex="0"
            role="button"
            class="btn btn-primary btn-circle avatar flex items-center"
          >
            <div class="w-12 rounded-full">
              {#if $currentUser?.avatar}
                <img
                  src={$currentUser?.avatar
                    ? getImageURL(
                        $currentUser?.collectionId,
                        $currentUser?.id,
                        $currentUser?.avatar,
                      )
                    : `https://ui-avatars.com/api/?name=${$currentUser?.email}`}
                  alt="User avatar"
                />
              {:else}
                <img
                  alt="Tailwind CSS Navbar component"
                  src="https://daisyui.com/images/stock/photo-1534528741775-53994a69daeb.jpg"
                />
              {/if}
            </div>
          </div>
          <ul
            class="mt-3 -z-[-1] p-2 shadow menu menu-sm border border-primary dropdown-content bg-base-100 rounded-box w-52"
          >
            <li><a class="font-bold" href="/">{$currentUser.email}</a></li>
            <a href="/my/settings/profile" class="justify-between">
              <li>
                Profile
                <span class="badge badge-info">New</span>
              </li>
            </a>
            <li><a href="/my/settings/account">Account</a></li>
            <li><a href="/my/settings/security">Settings</a></li>
            <form
              class="w-full flex mt-5"
              method="POST"
              action="/logout"
              use:enhance={() => {
                return async ({ result }) => {
                  pb.authStore.clear()
                  await applyAction(result)
                }
              }}
            >
              <button class="btn btn-primary btn-sm w-full">Log out</button>
            </form>
          </ul>
        </div>
      {:else}
        <div class="flex items-center text-sm">
          <div><a href="/login" class="btn btn-ghost">Log in</a></div>
          <div><a href="/register" class="btn btn-ghost">Register</a></div>
        </div>
      {/if}
    </div>
  </div>
</div>

<div class="max-w-xl mx-auto p-6">
  <slot />
</div>
