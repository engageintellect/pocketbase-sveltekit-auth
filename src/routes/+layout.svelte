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

<div class="h-full min-h-screen">
  <div class="bg-base-100 text-neutral-content">
    <div class="navbar max-w-xl mx-auto text-base-content">
      <div class="flex-1">
        <a href="/" class="btn btn-primary text-xl">spatz</a>
      </div>
      <div class="flex-none gap-2">
        {#if $currentUser}
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
              class="dropdown-content dropdown-end rounded-box border-primary bg-base-100 z-50 mt-3 h-96 w-52 overflow-auto border p-2 shadow"
            >
              <li
                class="sticky top-0 theme-controller text-primary-content btn btn-primary btn-sm btn-block justify-start font-medium mb-2"
              >
                <div class="flex items-center gap-2">
                  <Icon icon="mdi-done" class="w-5 h-5" />
                  {$selectedTheme}
                </div>
              </li>
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

          <div class="dropdown dropdown-end">
            <div
              tabindex="0"
              role="button"
              class="btn btn-primary btn-circle avatar flex items-center justify-center"
            >
              <div class="h-full w-full rounded-full">
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
                  <Icon
                    icon="mdi-account-circle"
                    class="h-full w-full scale-[110%] transition-scale duration-200 md:hover:scale-[105%] rounded-full text-base-100 "
                  />
                  <!-- <img
                    alt="Tailwind CSS Navbar component"
                    src="https://daisyui.com/images/stock/photo-1534528741775-53994a69daeb.jpg"
                  /> -->
                {/if}
              </div>
            </div>

            <ul
              tabindex="-1"
              class="dropdown-content menu p-2 shadow bg-base-100 rounded-box w-52 border border-primary mt-3 z-50"
            >
              <li class="mb-5">
                <div
                  class="bg-primary hover:bg-primary text-primary-content w-full truncate"
                >
                  <a href="/my/settings/profile" class="truncate"
                    >{$currentUser?.email}</a
                  >
                </div>
              </li>

              <li>
                <a href="/ai/chat">
                  <div class="flex gap-2">
                    <div>AI</div>
                    <div class="badge badge-accent">new</div>
                  </div>
                </a>
              </li>
              <li><a href="/my/settings/profile">Profile</a></li>
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
                <button class="btn btn-primary btn-sm w-full">
                  <div class="flex w-full items-center justify-between">
                    <div>logout</div>

                    <Icon icon="mdi-logout" class="w-5 h-5" />
                  </div>
                </button>
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

  <div class="max-w-xl mx-auto p-2 w-full">
    <slot />
  </div>

  <footer
    class="footer footer-center rounded bg-base-100 p-5 text-base-content"
  >
    <nav class="flex justify-center">
      <div class="flex gap-1">
        <div>Made with</div>
        <div><Icon icon="mdi:heart" class="h-5 w-5 text-red-500" /></div>
        by
        <a
          href="https://github.com/engageintellect"
          class="link-hover link underline">@engageintellect</a
        >
      </div>
    </nav>
  </footer>
</div>
