<template>
  <article class="card__container">
    <div class="header">
      <slot name="header"></slot>
    </div>

    <div class="body">
      <slot name="body"></slot>
    </div>

    <div class="footer">
      <slot name="footer"></slot>
    </div>
  </article>
</template>

<script>
export default {
  name: 'UserCard'
}
</script>

<style lang="scss" scoped>
article {
  margin-top: 3em;
  box-shadow: 0 0 5px 5px #eee;
  border-radius: 5px;
}
</style>
