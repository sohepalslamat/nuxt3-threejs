<template>
  <header>
    <NuxtLink to="/" :active-class="'active'">Home</NuxtLink>
    <NuxtLink to="/galaxy/" :active-class="'active'">Galaxy</NuxtLink>
    <NuxtLink to="/font/" :active-class="'active'">Font</NuxtLink>
    <NuxtLink to="/basketball/" :active-class="'active'">Basketball</NuxtLink>
  </header>
  <NuxtPage />
</template>
<script setup lang="ts">
  
</script>
<style>
header {
  position: relative;
  z-index: 1;
  display: flex;
  gap: 10px;
  padding: 4px;
}
header a {
  text-decoration: none;
    color: white;
    font-size: larger;
}
.active {
  color: aquamarine;
}
</style>

