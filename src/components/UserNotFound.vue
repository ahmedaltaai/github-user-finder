<template>
  <article class="notFoundCard__container">
    <slot name="notFound"></slot>
  </article>
</template>

<script>
export default {
  name: 'UserNotFound'
}
</script>

<style lang="scss" scoped>
article {
  margin-top: 3em;
  border-radius: 5px;
}
</style>
