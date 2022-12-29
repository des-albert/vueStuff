<template>
  <nav
    class="navbar is-success"
    aria-label="main navigation"
    role="navigation"
  >
  <div class="container is-max-desktop px-2">
      <div class="navbar-brand">
        <div class="navbar-item is-size-4 is-family-monospace">
          dgmsotc
        </div>
        <a
          @click.prevent="showMobileNav = !showMobileNav"
          class="navbar-burger"
          :class="{ 'is-active' : showMobileNav }"
          aria-expanded="false"
          aria-label="menu"
          data-target="navbarBasicExample"
          role="button"
          ref="navbarBurgerRef"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>
      <div id="navbarBasicExample" class="navbar-menu" :class="{ 'is-active': showMobileNav }" ref="navbarMenuRef">  
        <div class="navbar-end">
          <div class="control navbar-item">
            <label class="radio">
              <input type="radio" name="cycle" value="random" v-model="cycle" @change="handleRadio">&nbsp;Random
            </label> 
            <label class="radio"> 
              <input type="radio" name="cycle" value="sequence" v-model="cycle" checked @change="handleRadio">&nbsp;Sequence
            </label>   
          </div>

          <RouterLink 
            @click="showMobileNav = false"
            class="navbar-item" 
            to="/"
            active-class="is-active"
          >
            Home
          </RouterLink>

          <RouterLink
            @click="showMobileNav = false" 
            class="navbar-item" 
            to="/addPost"
            active-class="is-active"
          >
            Add Post
          </RouterLink>
        </div>
      </div>
    </div>         

  </nav>
</template>

<script setup>
  import { ref } from 'vue'
  import { storeToRefs } from 'pinia'
  import { onClickOutside } from '@vueuse/core' 
  import { useStorePosts } from '@/stores/storePosts'
 
  const storePosts = useStorePosts()
  
  const showMobileNav = ref(false)

  const navbarMenuRef = ref(null)
  const navbarBurgerRef = ref(null)

  const cycle = ref('false')
  const { random } = storeToRefs(storePosts)

  const handleRadio = () => {

    if (cycle.value === "random")
      random.value = true
    else 
      random.value = false
  }

  onClickOutside(navbarMenuRef, () => {
    showMobileNav.value = false
  }, {
    ignore: [navbarBurgerRef]
  })

</script>

<style>
@media (max-width: 1023px) {
  .navbar-menu {
    position: absolute;
    left: 0;
    width: 100%;
  }
}
</style>